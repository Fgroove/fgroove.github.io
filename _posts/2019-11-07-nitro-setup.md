---
Javalayout: post
title:   "nitro配置总结"
date:   2019-11-07 14:55:01 +0800
categories: "HWProject"
tag: "nitro"

---

* content
{:toc}




# nitro

nitro需要libvmi获取更多信息，libvmi的Python binding，因此涉及到github上面两个项目：

* [nitro]( https://github.com/KVM-VMI/nitro )
* [libvmi/python](https://github.com/libvmi/python)

# nitro

* 现版本
  * linux：4.9.0
  * qemu：2.8.0
* 目标版本
  * linux：4.10.2
  * qemu：2.6.0

## Using

对应的KVM/QEMU，recall, libvmi配置之后,执行`main.py`,参数为libvirt的domain name，用来绑定QEMU pid,

```
./main win7
```

## VM启动方式

### Virsh

### `qemu-system-x86_64`

[怎么查看用该命令启动的虚拟机？](https://askubuntu.com/questions/444849/how-to-list-all-virtual-machines-created-using-qemu-system-x86-64)

```shell
ps -ef | grep qemu-system-x86_64
```

显示`qemu-system-x86_64`启动的所有进程，进程号以及启动参数。

## Setup

### 1. python3、pip3

### 2. docopt

```shell
pip3 install docopt==0.6.2
```

### 3.libvirt

xml配置文件`tests/`下有模板。

#### python binding

```shell
sudo apt install python3-libvirt
```



### 4. Ioctl-opt Python3

```shell
pip3 install ioctl-opt==1.1
```

### 5.libvmi python bindings

后面会有`vmi_request_page_fault`，这个结果python binding里面有，nitro/libvmi里面没有

* 所以吧python binding里面的删除掉即可，
* [Merge pull request #42 from libvmi/add_vmi_request_page_fault](https://github.com/libvmi/python/commit/06068a873a823d4563eb08ce8d6c17947826b1f6),该commit回滚到之前就行

#### `libvmi`

* 依赖

```shell
sudo apt install cmake flex bison libglib2.0-dev libvirt-dev libjson-c-dev libyajl-dev
```

* 编译安装

```shell
mkdir build
cd build
cmake ..
make
sudo ldconfig
sudo make install
```

#### `libvmi/python`

* 依赖

```shell
pip3 install pkgconfig==1.0.0
pip3 install cffi
pip3 install future==0.16.0
```

* 编译安装

```shell
python3 setup.py build
sudo python3 setup.py install
```



其余的缺啥补啥。