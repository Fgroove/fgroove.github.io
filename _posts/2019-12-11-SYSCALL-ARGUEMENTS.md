---
Javalayout: post
title:   "修改系统调用参数"
date:   2019-12-11 08:55:01 +0800
categories: 基于动态的恶意软件分析
tag: nitro

---

* content
{:toc}






# [修改系统调用参数]( https://github.com/KVM-VMI/nitro/issues/8 )

以`NtOpenFile` MSDN为例：

```C
NTSTATUS NtOpenFile(
  _Out_ PHANDLE            FileHandle,
  _In_  ACCESS_MASK        DesiredAccess,
  _In_  POBJECT_ATTRIBUTES ObjectAttributes,
  _Out_ PIO_STATUS_BLOCK   IoStatusBlock,
  _In_  ULONG              ShareAccess,
  _In_  ULONG              OpenOptions
);
```

参数将以特定的方式传递给内核，具体取决于

* 操作系统调用协议
* 系统调用类型（syscall或sysenter）

## Windows/Linux syscall arguments passing

|       | Windows sysenter | Windows syscall | Linux sysenter | Linux syscall |
| ----- | ---------------- | --------------- | -------------- | ------------- |
| arg 1 | edx              | rcx             | ebx            | rdi           |
| arg 2 | edx + 4          | rdx             | ecx            | rsi           |
| arg 3 | edx + 8          | r8              | edx            | rdx           |
| arg 4 | edx + 12         | r9              | esi            | r10           |
| arg 5 | edx + 16         | rsp + ?         | edi            | r8            |
| arg 6 | edx + 20         | rsp + ? + 8     | ebp            | r9            |
| arg 7 | edx + 24         | rsp + ? + 16    | no             | no            |

## References

*  [Anatomy of a Linux system call part 1](https://lwn.net/Articles/604287/) 
* [Anatomy of a Linux system call part 2](https://lwn.net/Articles/604515/)
* [sysenter on Windows](http://www.osronline.com/article.cfm?id=257)

### 主要发现

* Linux系统调用最多有6个参数。
* Linux始终将寄存器用于syscall参数传递。
* Windows使用寄存器和内存。

## 修改系统调用参数

Nitro可以即时修改syscall参数并更改Guset的行为。

按照我们先前发现的内容，如果要提供这样的API，则必须涵盖2种情况：

* 参数存储在寄存器中
* 参数存储在内存中（Windows）

我们分别有2个解决方案：

* 利用`nitro_set_regs ioctl`替换寄存器值
* 利用libvmi的`vmi_write_va` API写入内存

### ArguementMap

```python
class ArgumentMap:

    CONVENTION = {
        SyscallType.syscall: [
            (SyscallArgumentType.register, 'rcx'),
            (SyscallArgumentType.register, 'rdx'),
            (SyscallArgumentType.register, 'r8'),
            (SyscallArgumentType.register, 'r9'),
            (SyscallArgumentType.memory, 5),
        ],
}
```

该参数可以映射到寄存器或内存中。

如果参数索引超出范围，则无论如何都会将其获取到内存中，因为我们无法确切知道Windows syscall允许的最大参数数量。