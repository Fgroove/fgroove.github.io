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
* [libvmi/python]( https://github.com/libvmi/python )

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

