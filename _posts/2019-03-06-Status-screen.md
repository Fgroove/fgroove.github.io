---
layout: post
title:  "Status Screen"
date:   2019-03-06 09:05:01 +0800
categories: 基于动态的恶意软件分析
tag: 反沙箱对抗技术

---
* content
{:toc}


# Status Screen

## Understanding the status screen

---

本文档提供了status screen的概述 - 以及对UI中显示的任何警告和红色文本进行故障排除的提示。 

更多信息，请参阅[README](http://lcamtuf.coredump.cx/afl/README.txt).

## status screen

---

![status_screen]({{"/styles/images/dynamic_Analysis/sandbox/3.jpg" | prepend: site.baseurl}})

① Process timing:Fuzzer运行时长、以及距离最近发现的路径、崩溃和挂起经过了多长时间。

② Overall results：Fuzzer当前状态的概述。

③ Cycle progress：我们输入队列的距离。

④ Map coverage：目标二进制文件中的插桩代码所观察到覆盖范围的细节。

⑤ Stage progress：Fuzzer现在正在执行的文件变异策略、执行次数和执行速度。

⑥ Findings in depth：有关我们找到的执行路径，异常和挂起数量的信息。

⑦ Fuzzing strategy yields：关于突变策略产生的最新行为和结果的详细信息。

⑧ Path geometry：有关Fuzzer找到的执行路径的信息。

⑨ CPU load：CPU利用率

## 0) 颜色说明

------

**Status screen**和**错误信息**使用各种颜色来保持可读性，标注最重要的细节。 例如，红色表示“咨询这个文档”。

但是，只有终端使用的是黑底白字"white on black"时，UI才能正常显示颜色。

## 1) Process timing

---

```
  +----------------------------------------------------+
  |        run time : 0 days, 8 hrs, 32 min, 43 sec    |当前fuzzer的运行时间
  |   last new path : 0 days, 0 hrs, 6 min, 40 sec     |最近一次发现新执行路径的时间
  | last uniq crash : none seen yet                    |最近一次崩溃的时间
  |  last uniq hang : 0 days, 1 hrs, 24 min, 32 sec    |最近一次超时的时间
  +----------------------------------------------------+
```

Timing: 大多数模糊测试应该预计会运行**数天或数周**；事实上，对于一个中等复杂的项目，第一次通过可能需要一天左右的时间。时不时地，一些工作将被允许运行数月。

Note: 如果工具在启动后几分钟内没有找到新路径，

* 可能是没有正确调用目标二进制文件，导致它永远无法解析我们正在抛出的输入文件;
* 另一种可能是默认内存限制`-m`限制太多，导致程序在很早就因分配缓冲区失败退出;
* 或输入文件明显无效，并始终无法进行基本标题检查（header check）。

如果一段时间内没有新路径出现，最终也会在此部分`last new path`显示一个大红色警告。

## 2) Overall results

---

```
  +-----------------------+
  |  cycles done : 0      |循环次数
  |  total paths : 2095   |总路径数
  | uniq crashes : 0      |崩溃次数
  |   uniq hangs : 19     |挂起次数
  +-----------------------+
```

本节中的第一个字段`cycles done`是目前为止完成的队列传递次数。即，模糊器目前为止遍历所有感兴趣测试用例的次数。应该允许每个模糊测试至少完成一个周期;理想情况下，应该跑很长时间。

如前所述，第一次遍历可能需要一天或更长时间。如果希望立即获得更广泛但更浅的覆盖范围，尝试`-d`选项,它通过跳过确定性模糊测试步骤提供更随便（非正式）的体验。然而，某些程度上不如标准模式。

为了帮助何时按Ctrl-C（结束测试），循环计数器`cycles done`采用颜色编码。它在**第一次通过时显示为`品红色`**，如果**在后续轮次中仍然显示新的发现，则进展为`黄色`**，然后**在结束时显示`蓝色`**;最后，在**模糊器长时间未看到任何动作后变为`绿色`**。

其余字段含义非常明显：目前为止发现路径的数量以及唯一故障的数量。可以通过浏览输出目录实时探索测试用例，崩溃和挂起，如README中所述。输出目录下包含`queues/`，`crashes/`和`hangs/`等文件夹，分别记录`total paths`,`crashes`和`hangs`路径信息。

## 3) Cycle progress

---

```
  +-------------------------------------+
  |  now processing : 1296 (61.86%)     |当前处理的路径ID（周期内的遍历进度）
  | paths timed out : 0 (0.00%)         |因持续超时而放弃的输入量
  +-------------------------------------+
```

此框告诉您模糊器与当前队列周期的距离：它显示当前正在处理的测试用例的ID，以及因持续超时而决定放弃的输入数量。

有时在第一行中显示的`*`后缀表示当前处理的路径不是“有利的”（稍后在第6节中讨论的属性）。

如果觉得模糊器的进度太慢，请参阅本文档第2部分中有关`-d`选项的说明。

## 4) Map coverage

---

```
  +--------------------------------------+
  |    map density : 10.15% / 29.07%     |位图密度
  | count coverage : 4.03 bits/tuple     |分支命中可变性
  +--------------------------------------+
```

目标二进制文件中的插桩代码所观察到覆盖范围的细节。

框中的第一行`map density`显示已经命中了多少个分支元组，与位图可以容纳的数量成比例。 **左边**的数字描述了**当前的输入**; **右边**的那个是**整个输入语料库的值**。

对于大量使用模板代码生成的非常复杂的程序，可能发生超过70％的`map density`。由于高位图密度使得模糊器难以可靠地识别新的程序状态,模糊器将标记红色的高百分比。

另一行`count coverage`处理二进制中看到的元组命中计数的可变性。 从本质上讲，如果对于所有输入，每个分支总是被命中固定的次数，则读为“1.00”。 当我们设法使其他的输入触发这些分支时，针将开始向“8.00”（8位命中图）移动，但可能永远不会达到那个极端。

总之，这些值可用于比较依赖于相同二进制文件的几个不同模糊测试作业的覆盖范围。

## 5) Stage progress

---

```
  +-------------------------------------+
  |  now trying : interest 32/8         |
  | stage execs : 3996/34.4k (11.62%)   |当前进度显示器
  | total execs : 27.4M                 |全局exec计数器
  |  exec speed : 891.7/sec             |当前程序执行速度
  +-------------------------------------+
```

`now trying`让您深入了解模糊器现在正在做什么。可以是下面任意值：

* `calibration` 预模糊测试阶段，检查执行路径以检测异常，建立基础执行速度等。无论何时进行新的查找，都会非常简单地执行该阶段。
* `trim L/S`  另一个预模糊测试阶段，测试用例被修剪为最短的形式，仍然会产生相同的执行路径。长度（L）和步长（S）通常与文件大小有关。
* `bitflip L/S` 确定性位翻转。在任何给定时间都有L位切换，以S位递增的方式遍历输入文件。目前的L / S变体是：`1/1,2/1,1/4,8/8,16/8,32/8`。
* `arith L/8` 确定性算术。模糊器试图将小整数减去或添加到8位，16位和32位值。步进总是8位。
* `interest L/8` 确定性值覆盖。模糊器有一个已知的*有趣的*8位，16位和32位值列表。步进是8位。
* `extras` 确定性地输入字典术语。这可以显示为`user`或`auto`，具体取决于模糊器是使用用户提供的字典`-x`还是自动创建的字典。您还将看到`over”`或`insert`，具体取决于字典单词是否覆盖现有数据，还是通过偏移剩余数据来插入以适应其长度。
* `havoc` a sort-of-fixed-length cycle with stacked random tweaks. 在此阶段尝试的操作包括位翻转，使用随机数和*有趣*整数覆盖，块删除，块重复，以及各种与字典相关的操作（如果首先提供字典）。
* `splice` 在没有新路径的第一个完整队列周期后启动的最后策略。它等同于'havoc'，除了它首先拼接来自队列任意选择中点的两个随机输入。
* `sync` 仅在设置`-M`或`-S`时使用（请参阅parallel_fuzzing.txt）。不涉及真正的模糊测试，但该工具会扫描其他模糊器的输出并根据需要导入测试用例。第一次可能需要几分钟左右。

## 6) Findings in depth

---

```
  +--------------------------------------+
  | favored paths : 879 (41.96%)         |模糊器偏好路径
  |  new edges on : 423 (20.19%)         |发现新路径的测试案例
  | total crashes : 0 (0 unique)         |崩溃计数器
  |  total tmouts : 24 (19 unique)       |超时计数器
  +--------------------------------------+
```

*写给书呆子部分*

该部分包括模糊器最喜欢的路径数量，这些路径基于代码中的最小化算法，以及实际导致更好的边缘覆盖的测试用例数量。 还有其他更详细的崩溃和超时计数器。

请注意，超时计数器与挂起计数器有些不同; 这个包括超过超时的所有测试用例，即使它们不足以被归类为挂起。

## 7) Fuzzing strategy yields

---

```
    从输入文件中删除的字节数 所需的`exec`数 不可删除无效字节
  +-----------------------------------------------------+
  |   bit flips : 57/289k, 18/289k, 18/288k             |
  |  byte flips : 0/36.2k, 4/35.7k, 7/34.6k             |
  | arithmetics : 53/2.54M, 0/537k, 0/55.2k             |
  |  known ints : 8/322k, 12/1.32M, 10/1.70M            |
  |  dictionary : 9/52k, 1/53k, 1/24k                   |
  |       havoc : 1903/20.0M, 0/0                       |
  |        trim : 20.31%/9201, 17.05%                   |
  +-----------------------------------------------------+
```

*写给书呆子部分*

本节中的修剪策略统计数据与其他部分略有不同。 此行中的第一个数字显示从输入文件中删除的字节数; 第二个对应于实现这一目标所需的`exec`数。 最后，第三个数字显示了字节的比例，这些字节虽然不可能删除，但被认为没有效果，并被排除在一些更昂贵的确定性模糊测试步骤之外。

## 8) Path geometry

---

```
  +---------------------+
  |    levels : 5       |模糊过程达到的路径深度
  |   pending : 1570    |尚未进行任何模糊测试的输入数量
  |  pend fav : 583     |模糊器的偏好条目
  | own finds : 0       |此模糊测试部分发现的新路径
  |  imported : 0       |并行模糊测试时从其他模糊实例导入条目
  | stability : 100.00% |路径一致性
  +---------------------+
```

本节中的第一个字段`level`跟踪通过引导模糊过程达到的**路径深度**。实质上：用户提供的初始测试用例被认为是“级别1”。通过传统模糊测试得出的测试用例被认为是“2级”;通过使用这些作为后续模糊回合的输入得出的是“3级”;等等。因此，**最大深度**代表了afl-fuzz所采用的插桩引导（instrumentation-guided）方法具有多少价值。

下一个字段`pending`显示尚未进行任何模糊测试的输入数量。对于在这个队列周期中模糊器真正想要达到的“偏好”条目，也给出了相同的统计数据`pend fav`（非偏好条目可能需要等待几个周期才能获得机会）。

接下来，在此模糊测试部分中找到了新路径的数量`own finds`，以及在进行并行模糊测试时从其他模糊实例中导入数量`imported`;以及相同输入有时在测试二进制中产生变量行为的程度。

最后一点实际上非常有趣：它测量观察到的路径的一致性。如果程序对于相同的输入数据总是表现相同，则它将获得100％的分数。当该值较低但仍显示为**紫色**时，模糊过程不太可能受到负面影响。如果它变成**红色**，你可能会遇到麻烦，因为AFL很难辨别调整输入文件后的表现是否有意义。

现在，大多数目标只会获得100％的分数，但是当你看到较低的数字时，参考官方文档。

## 9) CPU load

---

```
  [cpu: 25%]CPU利用率
```

本地系统上明显的CPU利用率：它是通过获取“可运行”状态中的进程数，然后将其与系统上的逻辑核心数进行比较来计算的。

如果该值以**绿色**显示，则表示您使用的CPU核心数量少于系统上可用的CPU核心数，并且可能可以并行化以提高性能; 有关如何执行此操作的提示，请参阅parallel_fuzzing.txt。

如果该值显示为**红色**，则您的CPU可能超额运行，运行其他模糊器可能不会给带来任何好处。

当然，这个基准非常简单; 它告诉你有多少进程准备好运行，但不知道它们可能是多少资源。 它也不区分物理内核，逻辑内核和虚拟化CPU; 这些中的每一个的性能特征会有很大差异。

如果需要更准确的度量，可以从命令行运行afl-gotcpu实用程序。

## 10) Addendum: status and plot files

对于未显示的操作，还可以在输出目录的`fuzzer_stats`文件中以机器可读格式找到一些关键状态屏幕信息。 这包括：

```
  - start_time     - unix time indicating the start time of afl-fuzz
  - last_update    - unix time corresponding to the last update of this file
  - fuzzer_pid     - PID of the fuzzer process
  - cycles_done    - queue cycles completed so far
  - execs_done     - number of execve() calls attempted
  - execs_per_sec  - current number of execs per second
  - paths_total    - total number of entries in the queue
  - paths_found    - number of entries discovered through local fuzzing
  - paths_imported - number of entries imported from other instances
  - max_depth      - number of levels in the generated data set
  - cur_path       - currently processed entry number
  - pending_favs   - number of favored entries still waiting to be fuzzed
  - pending_total  - number of all entries waiting to be fuzzed
  - stability      - percentage of bitmap bytes that behave consistently
  - variable_paths - number of test cases showing variable behavior
  - unique_crashes - number of unique crashes recorded
  - unique_hangs   - number of unique hangs encountered
```

其中大部分直接映射到前面讨论过的UI元素。

最重要的是，可以找到一个名为`plot_data`的条目，其中包含大多数这些字段的可绘制历史记录。 如果安装了`gnuplot`，可以使用附带的`afl-plot`工具将其转换为一个很好的**进度报告**。**制图工具。**



## 参考

[AFL官网status screen](http://lcamtuf.coredump.cx/afl/status_screen.txt)

[用AFL开始你的第一次Fuzzing](https://www.freebuf.com/articles/system/191536.html)

[American Fuzzy Lop使用](http://galaxylab.org/afl%E4%BD%BF%E7%94%A8101/)

