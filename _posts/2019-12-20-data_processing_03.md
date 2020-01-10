---
Javalayout: post
title:   "数据处理-PTpackets"
date:   2019-12-20 08:55:01 +0800
categories: "HWProject"
tag: "malwareAnalysis"

---

* content
{:toc}






# Intel Processor Trace

* **PSB：** `packet stream boundary`, 数据流边界`psb psbend`，同步到PT数据流的独特模式
* **TNT：**`taken/not-taken packet `，1比特记录一个条件跳转
* **TIP：** `target ip packet`记录间接跳转或者函数调用的目标地址
* **FUP：**`flow update packet` 

## 问题

PT不会记录直接跳转

## PT包

### MODE.EXEC

表示执行模式，比如16位、32位和64位等。

```
mode.exec cs.d 1
```

式通过称为`LMA`（长模式有效）的控制位激活。 禁用LMA后，处理器将以标准x86模式运行，并且将与16位和32位操作系统和应用程序兼容，也就是说，它将与当今存在的所有内容兼容。 

激活LMA（长模式）后，将启用64位扩展，从而提供一个新的64位CPU。 长模式也分为两个子模式：

* 64位模式和兼容模式。 

这两个子模式由D位和L位控制，这些位位于CS（代码段）寄存器所指向的描述符中。 兼容模式很有趣，因为它允许作为程序在64位模式下运行16位或32位应用程序。 它类似于386处理器的虚拟模式86。 图1显示了这些模式的说明。

|                            | CS.L=0 CS.D=0             | CS.L=0 CS.D=1             | CS.L=1 CS.D=0        | CS.L=1 CS.D=1        |
| -------------------------- | ------------------------- | ------------------------- | -------------------- | -------------------- |
| **LMA=0** **Legal Mode**   | Standard 16-bit Mode      | Standard 32-bit Mode      | Standard 16-bit Mode | Standard 32-bit Mode |
| **LMA=1 Long Mode Active** | 16-bit Compatibility Mode | 32-bit Compatibility Mode | 64-bit Mode          | Reserved             |

### MODE.TSX

```
mode.tsx
vmcs xxxxxxxxx
```

`tsx` transactional synchronization extensions

