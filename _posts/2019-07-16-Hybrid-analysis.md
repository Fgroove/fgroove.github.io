---
layout: post
title:   "Automated Hybrid Analysis of Android Malware.pdfe"
date:   2019-07-16 08:55:01 +0800
categories: "research proposal"
tag: "survey"


---

[TOC]



# [TMC'18] Automated Hybrid Analysis of Android Malware

## overview

我们的方法的重点是触发器(trigger)，它控制执行某些目标位置。给定目标位置，静态分析的第一个任务是通过在相应的程序路径上执行控制流分析和符号数据流分析来识别触发器。对于我们的示例，目标位置是恶意行为abortBroadcast （在第25行），因此我们识别六个触发器（在第4,7,11,15,23,24行）。为了协助以下按需强制执行，还提取了这些触发器所需的分支结果，这些结果将用于以下插桩阶段。

静态分析的第二个任务是插桩原始应用程序，并且将动态执行已插桩的应用程序。在我们的上下文中，插桩有多种用途。一方面，它将一些静态分析结果插入到应用程序中以帮助动态执行。另一方面，通过插入一些监控代码，可以提取许多动态值或执行信息，以便为进一步处理的静态分析提供进一步的反馈。例如，当转储动态加载代码或发生崩溃时，将执行检测以包括动态加载的代码或解决崩溃。插桩后的APK将进入新一轮的分析。

target是人为指定的，由target识别trigger。