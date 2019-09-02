---
 Javalayout: post
title:   "groupMeeting"
date:   2019-08-30 08:55:01 +0800
categories: "meeting"
tag: "groupMeeting"

---

* content
{:toc}




# groupMeeting

### Kvmi

根据已有的接口，编写监控程序

* 方便移植，没有太多应用

### nitro

应用libvmi，绑定`qemu2.8.0`

* 参考多，不好移植
* 未配置网络，`linux 4.9.0`下没有自动生成`virbr0`,virsh文件未配置网络。

`vmi_request_page_fault`python binding里面有，nitro/libvmi里面没有

* 添加到libvmi
* 回滚python binding

