---
Javalayout: post
title:   "nitro设计与实现"
date:   2019-11-19 08:55:01 +0800
categories: 基于动态的恶意软件分析
tag: "nitro"

---

* content
{:toc}




#nitro设计与实现

* 一个nitro事件意味着一个系统调用事件

1.获取虚拟机的`file descriptor`

2.获取vcpu fd列表

3.设置`syscall trap`

```python
for each vcpu
	vcpu_load
	change MSR register
	vcpu_put
```

4.等待虚拟机执行`syscall/sysenter`

5.生成`General Protection Fault/Undefined Opcode (#GP/#UD)` 

6.`vmx.c:handle_exception()`

7.`emulate_instruction`

8.`emulate.c:em_syscall/em_sysenter`

```python
nitro.event.present = True
```

9.`x86.c:vcpu_run()`

```python
if (nitro.event.present)
	report_event
```

10.`nitro_x86.c:report_event()`

```python
nitro_wait()
wait for userland to call get_event()
```

11.userland called`continue_vm()`



* SSDT`System Services Descriptor Table`,ssdt表就是把ring3的Win32 API和ring0的内核API联系起来。Windows有两个主要SSDT，NT和win32k。

## CR3功能实现

nitro维护了一个hashtable `CR3->Eprocess`，将目标程序加入该哈希表；然后再`nitro_x86.c:report_event()` 仅输出与目标程序匹配的系统调用。

