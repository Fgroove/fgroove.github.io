---
layout: post
title:  "IPT for enhanced analysis"
date:   2019-05-08 8:55:01 +0800
categories: 双视图+IPT"
tag: VMray
---
* content
{:toc}


# Nowhere to Hide: Analyzing Environment-Sensitive Malware with Rewind

---
[blog](https://www.vmray.com/cyber-security-blog/analyzing-environment-sensitive-malware/)

## Introducing VMRay Rewind

VMRay Rewind不仅可以可靠地检测环境敏感的恶意软件，还可以自动确定必要的环境设置并相应地修改基础分析环境。然后，在修改的环境中再次分析恶意软件并揭示隐藏的功能。

为了检测对环境敏感的恶意软件以及隐藏的功能，我们将英特尔新的PT功能与强大的分析技术和复杂的启发式技术相结合：

* 我们利用PT信息来确定被监视进程的内存dumps中的代码覆盖，即识别在分析期间尚未执行的所有代码位置。
* 从这些未触发的代码位置，我们识别“有趣”函数的子集，例如，通过丢弃错误处理例程。
* 然后，我们追溯从这些“有趣的”未执行代码位置到依赖于环境设置的先前条件分支（例如，获得当前时间或键盘布局的函数）的执行流程。
* 然后应用符号执行来识别隐藏函数的路径（条件）。
* 在这些路径上，我们使用求解器生成具体值以触发它们的执行。
* 最后，我们重新分析示例并确保所有环境查询都会产生达到隐藏功能所需的值。

请注意，所有步骤都是完全自主执行的。 无需用户交互来：
a）识别隐藏的功能
b）确定所需的环境条件
c）在包括所需条件的修改环境中重新运行分析

## 环境敏感恶意软件的自动分析

让我们来看看只有在特定时间段内执行恶意行为的恶意软件。 初步分析后，VMRay Analyzer会自动将收集的PT信息与创建的内存dump相结合。 请注意，dump不仅包括进程映像，它通常类似于二进制可执行文件的解压缩表示形式，但可选地包括所有堆栈内存。

为了更好地说明，我们应用一个脚本，根据收集的PT数据自动污染IDA反汇编程序输出。 图1显示了这种受污染的IDA视图。 你可以很容易地看到：

* 恶意软件通过调用`GetSystemTime()`API来检索当前日期和时间。
* 然后它将当前月份与`0x0B(11月)`和当前年份与`0x7da(2010)`进行比较。
* 由于未达到预期日期，恶意软件会立即通过调用`ExitProcess()`API终止。

在污染之后我们开始真正的工作：

最初，我们的算法识别从`loc_40915C`开始的代码序列，因为它在运行期间尚未到达。 然后它追溯并识别可能导致执行此代码的两个条件分支。 它还将API `GetSystemTime`标识为已检查的分支条件的数据源。 最后，它确定`SYSTEMTIME`结构中的`MONTH`和`YEAR`字段是重要的路径条件变量。

有两种不同的执行路径导致隐藏功能，并且我们的算法都决定了特定的路径条件：

```plain
CASE 1:
path from GetSystemTime at address 0x75c0d354
reached interesting code sequence at 0x40915C
Required conditions:
SYSTEMTIME.wMonth 0xa
```



```plain
CASE 2:
path from GetSystemTime at address 0x75c0d354
reached interesting code sequence at 0x40915C
Required conditions:
SYSTEMTIME.wMonth 0xb
SYSTEMTIME.wYear 0x7d9
```

有了这些信息，VMRay Analyzer可以针对每个已识别的路径条件在调整的环境中重新运行样本。 如图4所示，这提供了更全面的分析结果，并且更多的恶意行为反映在由VTI（VMRay威胁标识符）引擎计算的总严重性分数中。

## 自动识别间接分支目标

Rewind的一个不错的副作用是它能够自动识别间接跳跃的目标并使用这些信息丰富反汇编。 这可能听起来模糊不清，仅对VMRay输出的静态后处理感兴趣。 但是，对于数字取证和相关APT的深入分析来说，这是一个很好的节省时间。

正如在`loc_b21be6`中的图5中所见，有一`call ecx`指令。 仅通过静态分析，它是相当耗时，复杂的，并且通常不可能确定揭示实际呼叫目标所需的具体ecx值。 但是，通过掌握PT数据，VMRay可以轻松确定目标。

图5显示了生成的反汇编注释：有两个调用目标，每个循环迭代一个：sub_B22164和sub_B24339。

