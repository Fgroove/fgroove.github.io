---
 Javalayout: post
title:   "env setup"
date:   2019-09-09 08:55:01 +0800
categories: "Sandbox Evasion"
tag: "linux-qemu"

---

* content
{:toc}




# linux环境设置总结

## KVM

* 内核4.10.2,需要打补丁
* 安装依赖

```shell
$ sudo apt-get install bc fakeroot flex bison libelf-dev libssl-dev ncurses-dev
```

* 配置内核

```shell
$ cd linux-4.10.2
$ make olddefconfig
$ make menuconfig
```

* 建立和安装内核

```shell
$ make -j4 bzImage
$ make -j4 modules
$ sudo make modules_install
$ sudo make install
```



## QEMU

* Qemu-2.6.0, 需要打补丁
* 安装依赖

```shell
$ sudo apt-get install libpixman-1-dev pkg-config zlib1g-dev libglib2.0-dev dh-autoreconf libspice-server-dev libsdl1.2-dev
```

* 配置创建和安装QEMU

```shell
$ cd qemu-2.6.0
$ ./configure --target-list=x86_64-softmmu --enable-sdl --prefix=/usr/local
$ make -j4
$ sudo make install
```


## libvirt

```shell
sudo apt install libvirt-bin libvirt-dev bridge-utils virt-manager virt-viewer
```



```shell
virsh define setup.xml
virsh list --all
virsh start win7
virsh shutdown win7
gvncviewer 127.0.0.1:0
virsh undefine win7
```

