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

### [Virsh](https://www.cnblogs.com/hukey/p/11246126.html)

### 连接hypervisor

`virsh connect [*hostname-or-URI*] [--readonly]`

* `virsh connect qemu:///system`，作为root用户连入
* `qemu:///session`,作为用户连入

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
pip install docopt==0.6.2
```

**注意：**这里需要pip/pip2安装docopt，因为是rekall需要使用。包括后续依赖future，需要pip/pip2安装`future==0.16.0`。

### 3.libvirt

xml配置文件`tests/`下有模板。虚拟机名字要和`nitro/libvmi.conf`里面的一致。

#### python binding

```shell
sudo apt install python3-libvirt
```

### virsh创建虚拟机

```shell
virsh define win7x86.xml
virsh start domain
virsh reboot domain
virsh shutdown domain
gvncviewer 127.0.0.1:0
```

重点配置好xml文件就可以了。

### 网络设置

```shell
sudo apt install bridge-utils -y
```

配置`/etc/network/interfaces`, `eno1`替换成网络接口

```shell
sudo vim /etc/network/interfaces

auto lo eno1 br0
iface lo inet loopback

iface eno1 inet manual

iface br0 inet dhcp #set this to static if required
   bridge_ports eno1
```

`enp0s10`示意：

```plain
enp0s10:
| | |
v | |
en| |   --> ethernet
  v |
  p0|   --> bus number (0)
    v
    s10 --> slot number (10)
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

* 配置`nitro/libvmi.conf`, 配置要监控的虚拟机名字

```cfg
win7x64 {
    ostype      = "Windows";
    win_tasks   = 0x188;
    win_pdbase  = 0x28;
    win_pid     = 0x180;
    win_pname   = 0x2e0;
}
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

### rekall

由于作者作者脚本引用的是Python2版本的rekall，所以要使用pip/pip2安装。

```shell
sudo pip3 install --upgrade setuptools pip wheel
sudo pip3 install rekall
```

安装完rekall之后一定先运行一下`rekall`,如果运行成功再运行nitro。

其余的缺啥补啥。



## TypeError

```shell
sudo python3 main.py win7x64
```

如果带上`--nobackend`一切正常，没有的话会出现问题

```shell
TypeError: NewZipFile() takes exactly 3 arguments (2 given)
```

[TypeError:XXX takes exactly 3 arguments (2 given)](https://github.com/google/rekall/issues/495)

由于aff4升级出现的问题，rekall使用的`PyAFF4==0.26.post6`

```shell
pip install PyAFF4==0.26.post6
```

