---
Javalayout: post
title:   "数据处理-PTpackets"
date:   2019-12-17 08:55:01 +0800
categories: "HWProject"
tag: "malwareAnalysis"

---

* content
{:toc}






## PT包

### MODE.EXEC

```intel pt

```



长模式通过称为`LMA`（长模式有效）的控制位激活。 禁用LMA后，处理器将以标准x86模式运行，并且将与16位和32位操作系统和应用程序兼容，也就是说，它将与当今存在的所有内容兼容。 

激活LMA（长模式）后，将启用64位扩展，从而提供一个新的64位CPU。 长模式也分为两个子模式：

* 64位模式和兼容模式。 

这两个子模式由D位和L位控制，这些位位于CS（代码段）寄存器所指向的描述符中。 兼容模式很有趣，因为它允许作为程序在64位模式下运行16位或32位应用程序。 它类似于386处理器的虚拟模式86。 图1显示了这些模式的说明。

|                            | CS.L=0 CS.D=0             | CS.L=0 CS.D=1             | CS.L=1 CS.D=0        | CS.L=1 CS.D=1        |
| -------------------------- | ------------------------- | ------------------------- | -------------------- | -------------------- |
| **LMA=0** **Legal Mode**   | Standard 16-bit Mode      | Standard 32-bit Mode      | Standard 16-bit Mode | Standard 32-bit Mode |
| **LMA=1 Long Mode Active** | 16-bit Compatibility Mode | 32-bit Compatibility Mode | 64-bit Mode          | Reserved             |

