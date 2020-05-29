---
layout: post
title:   "应用符号执行求解路径条件"
date:   2020-05-28 08:55:01 +0800
categories: "HWProject"
tag: "Intel PT"

---

* content
{:toc}






# 应用符号执行求解路径条件

## angr

近几年，随着开源社区的壮大，涌现出了一批优秀的符号执行引擎。

| 符号执行引擎 | 特点                                                         |
| ------------ | ------------------------------------------------------------ |
| angr         | 可以对范围内的函数进行符号执行，即选择性符号执行。  用户友好的Python接口，说明文档齐全，广泛的应用案例。 |
| KLEE         | KLEE对Linux二进制文件更友好。当前的开源KLEE仅适用于整个程序范围的符号执行。 |
| Triton       | 基于PIN的符号执行框架，Triton有一个很好的污点分析引擎，提供了用户友好的Python接口。但仅适用于64位程序。 |
| S2E          | 提出了选择性符号执行的概念，没有Python接口。                 |

angr发布在Blackhat 2015上，是由UCSB的安全研究人员开发的二进制分析工具，在DARPA网络大挑战赛中被广泛使用。在其符号执行引擎的支持下，angr友好的Python接口便于用户编写脚本，本文采用angr作为符号执行引擎来确定相关代码路径条件。

## angr的安装和使用

angr可以通过pip安装！建议将其安装在virtualenv中，

```shell
$ mkvirtualenv angr
$ pip install angr
```

也可以通过docker安装，

```shell
$ docker run -it angr/angr
```

### angr关键方法和参数

* `angr.Project():` 加载二进制文件，在 angr 中，基本上所有的对象操作都依赖于已有的 Project。常见形式如下，`load_options`取消自动加载库函数，防止符号执行卡死。

```python
p = angr.Project('./sample', load_options={'auto_load_libs': False})
```

* `factory`，即 `AngrObjectFactory`，提供重要分析对象的接口，如 `blocks / state / SimulationManager` 等。
  * `factory.blank_state(addr):` 将addr处作为初始状态。
  * `state.regs / state.mem:` 访问寄存器和内存信息。
  * `SimulationManager:` 用于管理state，运行模拟等。常用`explore()`方法探索目的路径，可行路径状态保存在`found` 数组变量中。
* `state.solver：` 访问求解引擎，angr 的求解引擎是 `claripy` 用于求解约束。
  * `state.solver.BVS("symbol", size):` 创建符号变量。
  * `solver.eval(expression):` 约束求解。

## Shamoon路径条件求解

Shamoon，真实恶意软件，

* 恶意软件通过API  `GetSystemTime()`来获取当前日期和时间。

* 然后将当前日期和时间与预设日期和时间（2012/8/15 8:08）进行比较。未达到预期日期，则返回值2，不触发恶意行为。

我们目的是获取程序`0x004032E3`到`0x004032FD`处代码块路径上的判断条件。首先在Python中初始化angr环境，然后加载二进制文件。

```python
import angr
angr.Project(".../shamoon", load_options={'auto_load_libs':False})
state=p.factory.blank_state(addr=0x4032E3)
```

通过IDA PRO获取程序`0x004032E3`和`0x004032FD`处代码块，识别路径上的条件分支，确定API `GetSystemTime()`是分支条件的数据源，并得到相关路径条件变量为SYSTEMTIME结构中的wMonth和wYear字段。

创建month和year的符号变量，

```python
state.memory.store(state.regs.ebp-0x14, state.solver.BVS("year", 8*2))
state.memory.store(state.regs.ebp-0x16, state.solver.BVS("month", 8*2))
```

符号执行，探索到`0x004032FD`处基本块的可行路径，将路径状态存储，

```python
sm = p.factory.simulation_manager(state)
sm.explore(find=0x4032FD)
s = sm.found[0]
```

最后，约束求解求出，变量值

```python
s.solver.eval(s.memory.load(state.regs.ebp-0x16, 2),cast_to=bytes)
```

最终求得路径条件值wYear=2012，wMonth=8.



## 参考文献

[1] angr. [https://angr.io/](https://angr.io/)

[2] angr API documentation. [https://angr.io/api-doc/index.html](https://angr.io/api-doc/index.html)

[3] Smashing Flare-On #2 with Concolic Testing. [http://0x0atang.github.io/reversing/2015/09/17/flareon2-concolic.html](http://0x0atang.github.io/reversing/2015/09/17/flareon2-concolic.html)

[4] angr文件与参数. [http://ysc21.github.io/page/2/](http://ysc21.github.io/page/2/)

