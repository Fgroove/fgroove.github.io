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

## RealBench

RealBench是实际CPU基准测试的另一个例子。 它使用了四个测试，都涉及一些容量的渲染：图像编辑，H.264视频编码，OpenCL和重型多任务处理。

您可以将您的发现上传到RealBench网站，以比较您与其他基准硬件配置的位置。 可能RealBench的最佳方面是它模拟常规课程负载; 没有压力测试将CPU推到最大，以便衡量其性能。 当然，压力测试也是RealBench中的一项可用功能。

RealBench无法在原生TCG模式下运行。

## Microsoft Windows benchmarks
### BAPCo: MobileMark, SYSmark, WebMark
---
**收费**

BAPCo，Business Applications Performance Corporation，是一个非营利性联盟（成立于1991年），其章程旨在为基于流行软件应用程序和操作系统的个人计算机开发和分发一套客观的性能基准。

BAPCo目前的成员包括宏碁，ARCIntuition，ChinaByte，CNET，仁宝电子，戴尔，惠普，日立，英特尔，LC未来中心，联想，微软，西部数据，纬创，三星，索尼，东芝，佐尔。

2011年6月21日，AMD公司宣布，在未能通过SYSmark 2012基准测试后，它已从BAPCo组织辞职[1]。 Nvidia和VIA也在几周后离开了。

### Futuremark: 3DMark, PCMark
PCMark: [Result](https://www.3dmark.com/pcm7/1173102#)

```shell
PCMark score395.0
Video playback and transcoding / Video playback
0.87867 fps
Video playback iteration 1
0.87302 fps
Video playback iteration 2
0.88872 fps
Video playback iteration 3
0.87427 fps
Video playback and transcoding / Video transcoding - downscaling
82.96312 kB/s
Video transcoding - downscaling iteration 1
81.73493 kB/s
Video transcoding - downscaling iteration 2
82.24995 kB/s
Video transcoding - downscaling iteration 3
84.90447 kB/s
System storage - gaming / System storage - gaming
6.10033 MB/s
System storage - gaming iteration 1
2.95645 MB/s
System storage - gaming iteration 2
6.52244 MB/s
System storage - gaming iteration 3
8.82211 MB/s
Graphics - DirectX 9 / Graphics - DirectX 9
1.0 fps
Graphics - DirectX 9 iteration 1
1.0 fps
Graphics - DirectX 9 iteration 2
1.0 fps
Graphics - DirectX 9 iteration 3
1.0 fps
Image manipulation / Image manipulation
3.08844 Mpx/s
Image manipulation iteration 1
3.09191 Mpx/s
Image manipulation iteration 2
3.08052 Mpx/s
Image manipulation iteration 3
3.09289 Mpx/s
System storage - importing pictures / System storage - importing pictures
12.03385 MB/s
System storage - importing pictures iteration 1
7.85247 MB/s
System storage - importing pictures iteration 2
8.28906 MB/s
System storage - importing pictures iteration 3
19.96 MB/s
Web browsing and decrypting / Web browsing
0.40673 pages/s
Web browsing iteration 1
0.39544 pages/s
Web browsing iteration 2
0.39992 pages/s
Web browsing iteration 3
0.42483 pages/s
Web browsing and decrypting / Data decrypting
3.61821 MB/s
Data decrypting iteration 1
3.59166 MB/s
Data decrypting iteration 2
3.63114 MB/s
Data decrypting iteration 3
3.63184 MB/s
System storage - Windows Defender / System storage - Windows Defender
2.99561 MB/s
System storage - Windows Defender iteration 1
1.45878 MB/s
System storage - Windows Defender iteration 2
2.32762 MB/s
System storage - Windows Defender iteration 3
5.20043 MB/s

```

```shell
SETTINGS
Default settings used Yes Repeat count3
```

### PiFast
### SuperPrime
### Super PI
### UserBenchmark
### Whetstone
### Windows System Assessment Tool, included with Windows Vista and later releases, providing an index for consumers to rate their systems easily
### Worldbench (discontinued)