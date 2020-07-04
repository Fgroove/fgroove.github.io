---
 Javalayout: post
title:   "WinDriver"
date:   2019-09-03 08:55:01 +0800
categories: 基于动态的恶意软件分析
tag: "Intel PT"

---

* content
{:toc}




# winDriver

## preparation

VS 2017

SDK & WDK(paired)  win10

SDK: 10.0.17134.0

WDK:1803版

cc##

## cmd

sc create name binPath= ".sys"

sc start name

## Q

qemu-img resize filename +10G doesn't work



## QEMU 2.11.2

Repeat it under QEMU 2.11.2sudo

***Notice: SDL Support yes***

### Win7 VM

```shell
qemu-img create -f qcow2 windows7.qcow2 40G

sudo qemu-system-x86_64 -smp 1 -m 4096 --enable-kvm windows7.qcow2 -cdrom Win_Pro_7_64.ISO 

sudo qemu-system-x86_64 -smp 1 -m 4096 --enable-kvm windows7.qcow2 
```



### VS

### WDK

Generate driver

### SC

```shell
sc create name binPath= ".sys"

bcdedit /set testsigning on/off //无签名测试模式,重启生效

sc start name
```



### Check

```shell
dmesg
```



### Notice

- SDK must be paired with WDK, could be set in driver property.
- Win7 must be 64 bit.

### Q

#### 1 qemu2.11.2 while qemu-img is version 2.11.1

```shell
cc@Ubuntu-18:~/qemu-2.11.2$ qemu-img --version
qemu-img version 2.11.1(Debian 1:2.11+dfsg-1ubuntu7.9)
Copyright (c) 2003-2017 Fabrice Bellard and the QEMU Project developers
```

## shell

dmesg



qemu-img create -f qcow2 windows7.qcow2 40G

sudo qemu-system-x86_64 -smp 1 -m 4096 --enable-kvm windows7.qcow2 -cdrom Win_Pro_7_64.ISO 

sudo qemu-system-x86_64 -smp 1 -m 4096 --enable-kvm windows7.qcow2 