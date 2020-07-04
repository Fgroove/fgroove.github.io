---
Javalayout: post
title:   "专利-基于HYPERVISOR的监控系统"
date:   2019-11-11 08:55:01 +0800
categories: 基于动态的恶意软件分析
tag: "nitro"

---

* content
{:toc}




## memo

Patent:`METHOD AND APPARATUS FOR HYPERVISOR BASED MONITORING OF SYSTEM INTERACTIONS `

* BarklyProtects,Inc. 

## KVM层面

`Extended Feature Enable Register(EFER) `是用来启用`SYSCALL/SYSRET`的寄存器。

Intel `IA-32e`和`64`位在ISA引入了`syscall`和`sysret`，以及一些型号指定寄存器MSRs，`IA32STAR`和`IA32LSTAR `。

* `IA32STAR`保存着`CS(code segment)`寄存器的值，表示代码在内存中的存储位置。
* `IA32LSTAR `保存着`RIP(Register Instruction Pointer)`寄存器的值，表示syscall指令的目标地址。
* RCX寄存器保存着sysret的返回地址。

EPT

* EPT可以设置内存页面可执行不可读，即处理器可以执行该页面指令但是访问数据。

Trap

* trap机制允许hypervisor捕获VM对`IA32LSTAR `MSR任何的读写操作。hypervisor可以修改或模拟对MSR的读写。



