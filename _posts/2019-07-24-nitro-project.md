---
layout: post
title:   "Selective Symbolic Execution"
date:   2019-07-24 13:55:01 +0800
categories: "path exploration"
tag: "nitro"
---

* content
{:toc}




#nitro

## overview

改新昂木增加了VMI到KVM hypervisor来监控一个运行的虚拟机，而且不需要客户机代理。

项目四个组件：

* kvm: linux 4.9.0, 修改kvm模块支持vmi
  * 必须手动安装内核，因为不是单独针对kvm模块的修改

* qemu：qemu 2.8.0，修改使支持内省
* nitro：用户空间库，用来接收事件、内省虚拟机状态，弥补语义鸿沟
* libvmi：跨Xen和kvm的统一API的vmi库

### 安装

* `kvm`下编译新内核
* `nitro`下设置用户空间内容，拦截系统调用
* 编译`qemu`

### nitro

#### 依赖

- `python 3`
- `docopt`，Python命令行参数解释器
- [`libvirt`](https://www.ibm.com/developerworks/cn/linux/l-libvirt/index.html)，提供一种虚拟机监控程序不可知的 API 来安全管理运行于主机上的来宾操作系统。
- `ioctl-opt Python 3`
- `cffi Python3` (optional)
- `libvmi` (optional)
- `rekall` (optional)

#### 使用

* 确保已加载修改的kvm模块，`cd kvm-vmi && make modules && make reload`
* 开启要监控的虚拟机
* 开启nitro `./main.py <vm_name>`

``` python
"""Nitro.

Usage:
	nitro [options] <vm_name>
	
Options:
  -h --help     Show this screen
  --nobackend   Don't analyze events
  -o --output   Output file (stdout if not specified)
"""
```

Nitro通过激活kvm里面的陷阱来监控`<vm_name>`的系统调用。

只安装了必须的依赖的话，需要在运行nitro时添加`—nobackend`.

`CTRL+C`打断运行，Nitro重置陷阱并将捕获到的时间写进`events.json`。`--out`输出到指定文件。

### libvirt

libvirt安装，官网下载源文件编译(http://libvirt.org/)

```shell
# 保持对操作系统发型版中安装可执行程序和共享库的目录的一致性
$ ./autogen.sh --system
$ make
$ sudo make install
```

依赖缺啥装啥,比如`gnutls`库，

```shell
sudo apt-get install gnutls-doc gnutls-bin
sudo apt-get install libgnutls28-dev libneon27-gnutls libcurl4-gnutls-dev
```

`libnl`库

```shell
sudo apt install libnl-3-dev libnl-route-3-dev
```

### [libvmi](https://www.cnblogs.com/ccxikka/p/9694888.html)

专注于读写虚拟机内存的自省库，监视虚拟机底层的运行细节并将其还原，支持Xen和KVM；

针对KVM虚拟化平台，libvmi对QEMU进行修改以提供虚拟机物理内存的读写接口，基于libvmi的自省程序通过本地Unix socket与QEMU通信，实现对特定物理地址内容的读写。

以获取虚拟机某个GPA内容为例，简述在KVM虚拟化平台下使用libvmi自省的流程：

1. `VMI Application`创建VMI实例，给出目标虚拟机名称及对应GPA，交由`libvmi`处理；
2. `libvmi`初始化vmi实例，然后使用QMP向`qemu-kvm-patch`发送创建`Unix socket`的命令，用于和目标虚拟机所在的qemu进程进行通信；
3. `qemu-kvm-patch`接收到QMP命令后，创建`Unix socket`，作为服务器端监听libvmi请求；
4. `libvmi`向`socket server`发出连接请求，内存读写请求；
5. `qemu-kvm-patch`接收来自`libvmi`的请求，调动qemu中相关函数进行自省操作，返回给`libvmi`；
6. `libvmi`接收`qemu-kvm-patch`返回的数据结果，将其返回`VMI Application`，此次内省操作结束。

```shell
./autogen.sh
./configure --disable-xen
make

su -
make install
ldconfig
exit
```

事先没有安装好`libvirt`，`./configure`之后kvm后面显示不支持，缺失`libvirt`。



