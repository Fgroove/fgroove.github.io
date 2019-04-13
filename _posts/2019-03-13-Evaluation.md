---
layout: post
title:  Evaluation
date:   2019-03-13 14:55:01 +0800
categories: "QEMU-KVM系统"
tag: 系统评估
---
* content
{:toc}


# Evaluation
---
在驱动里面：

**1.Driver.c里修改MalwareName为你要监控的进程名称。**

2.目前我是用`MalwareImageBase`和`MalwareImageSize`这两个变量标识而已进程exe文件的，只能标识包含1个exe，你们参考`xen bus`的代码，把里面的那个链表移植过来，可以存储多个exe的信息。

3.修改`LoadImageNotifyRoutine`和`ProcessNotify`，监听程序创建和镜像加载的函数，恶意进程创建时，保存它的eprocess，进程exe镜像加载时，将exe的信息存储在链表中，卸载时，不用考虑。

4.在`__ept_handle_violation`里面。首先判断当前的进程是不是恶意进程（通过eprocess判断，我已经写好了），然后看虚拟地址在不在你的链表之中，如果在的话说明是恶意程序的exe，修改权限为ACCESS_MALWARE就行了

## SPEC CPU2006

## PCMark 7

