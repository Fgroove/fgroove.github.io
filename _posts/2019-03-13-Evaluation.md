---
layout: post
title:  Evaluation
date:   2019-03-13 14:55:01 +0800
categories: 基于动态的恶意软件分析
tag: QEMU-KVM系统
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

## Trouble

安装完PCMark7/SPEC CPU2006后，加载驱动卡死；

进一步判断和更改MalwareName关系,没有关系。

---

```plain
重启多次之后,磁盘空间变小
镜像原始剩余可用空间：9.18G 
重启后可用空间：7.50GB
CPU使用率50-100%，内存90% 什么应用都没打开
```

```plain
framebuf 蓝屏dump一次多了4G内存

加载驱动之后，启动PCMark7没有tcg和kvm切换，这种情况下PCMark7也跑不完
```

```plain
镜像不太稳定，一次执行太多操作（比如虚拟机卡顿时安装软件），重启后驱动加载失败，文件系统出错
```





## SPEC CPU2006

## PCMark 7

