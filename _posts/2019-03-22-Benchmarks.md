---
layout: post
title:  Benchmarks
date:   2019-03-22 8:55:01 +0800
categories: "QEMU-KVM系统"
tag: 系统评估
---
* content
{:toc}


# Benchmarks for Windows

## CineBench
---
CineBench提供了最全面，最值得信赖的CPU基准测试之一。 它使图像渲染成为一项主要由CPU承担的任务 - 并将其与其他实际测试进行比较，以衡量CPU的性能。

这是真实世界：虽然其他基准测试将测试您的整体PC性能或CPU和GPU的组合，但CineBench专门测试CPU的所有可用处理器内核。

运行测试后，您的处理器将按分数进行分级：分数越高，CPU的性能输出就越强。

QEMU(with kvm)的CINEBENCH SCORES: 158pts

```shell
Your System: 
Rendering: 158pts
Rendering (Single Core): 158pts
Multi Processor Ratio: 1.00x
1 Core, 1 Thread @ 2.81 GHz
QEMU Virtual CPU version 2.5+
Windows 7, 64 bit, Professional Edition Service Pack 1 (build 7601)
CINEBENCH Info:
CINEBENCH Version: 15.037 RC83328demo 64 Bit
```

QEMU(no kvm)的CINEBENCH SCORES: 5pts

```shell
Your System: 
Rendering: 5pts
Rendering (Single Core): 5pts
Multi Processor Ratio: 1.00x
1 Core, 1 Thread @ 2.81 GHz
QEMU Virtual CPU version 2.5+
Windows 7, 64 bit, Professional Edition Service Pack 1 (build 7601)
CINEBENCH Info:
CINEBENCH Version: 15.037 RC83328demo 64 Bit
```

### KVM-TCG

R20可以正常跳转，但是启动不了；

R15可能名字`CINEBENCH Windows 64 Bit.exe`包含空格，无法处理；改名监控成功后，有切换但是无法启动。

## RealBench

---

RealBench是实际CPU基准测试的另一个例子。 它使用了四个测试，都涉及一些容量的渲染：图像编辑，H.264视频编码，OpenCL和重型多任务处理。

您可以将您的发现上传到RealBench网站，以比较您与其他基准硬件配置的位置。 可能RealBench的最佳方面是它模拟常规课程负载; 没有压力测试将CPU推到最大，以便衡量其性能。 当然，压力测试也是RealBench中的一项可用功能。

### *注*

RealBench无法在原生TCG模式下运行。

RealBench起两个同名exe，一个图形界面，一个log output界面

## Microsoft Windows benchmarks
### BAPCo: MobileMark, SYSmark, WebMark
---
**收费**

BAPCo，Business Applications Performance Corporation，是一个非营利性联盟（成立于1991年），其章程旨在为基于流行软件应用程序和操作系统的个人计算机开发和分发一套客观的性能基准。

BAPCo目前的成员包括宏碁，ARCIntuition，ChinaByte，CNET，仁宝电子，戴尔，惠普，日立，英特尔，LC未来中心，联想，微软，西部数据，纬创，三星，索尼，东芝，佐尔。

2011年6月21日，AMD公司宣布，在未能通过SYSmark 2012基准测试后，它已从BAPCo组织辞职[1]。 Nvidia和VIA也在几周后离开了。

### Futuremark: 3DMark, PCMark
#### PCMark: [Result](https://www.3dmark.com/pcm7/1173102#)


```shell
SETTINGS
Default settings used Yes Repeat count3
```

多exe文件

#### 3DMark

破解版 失败 收费才是正道

### PiFast
手动输入参数
### SuperPrime

韩国的，找不到

### Super PI
### UserBenchmark

[KVM-TCG](https://www.userbenchmark.com/UserRun/16326749)



### Whetstone
### Windows System Assessment Tool, included with Windows Vista and later releases, providing an index for consumers to rate their systems easily
### Worldbench (discontinued)

## 其他

### 鲁大师

娱乐大师，主要是其跑分浮动大，可比性不强，不能真实反映性能指标。

主要是集成了几个功能比较方便：第一查看硬件，第二安装驱动，第三压力测试，第四跑分。虽然都不算一流，单是结合到一起就是个方便。

### 国际象棋大师

### WINRAR

### CPU-Z

### VMmark

### Geekbench

### Thread Bench 2010

[Thread Bench](https://astro.temple.edu/~drhoads/subsites/cpu/index.html)

| Benchmark     | 进程个数 | 备注                              |
| ------------- | -------- | --------------------------------- |
| SPEC CPU2006  | 多个     | 单个benchmark跑的时候也有多个进程 |
| PCMark7       | 多个     |                                   |
| UserBenchMark | 多个     | 应该是每个单项都有exe             |
| RealBench     | 多个     |                                   |
| Cinebench     | 一个     | 正常切换，就是打不开程序          |
| ThreadBench   | 一个     | 无法检测到进程(名称有错?)         |
| SuperPI       | 一个     | 正常测试                          |
|               |          |                                   |

## demo

驱动监控程序/进程名长的话无法loadImage（PCMark7，SuperPI），过长无法检测到进程创建（ThreadBench）。

改程序名，长度为4都成功了。或者是和大小写有关。

