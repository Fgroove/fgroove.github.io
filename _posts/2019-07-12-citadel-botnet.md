---
layout: post
title:   "Dumps"
date:   2019-07-12 08:55:01 +0800
categories: "research proposal"
tag: "技术思路"

---

* content
{:toc}




# Dumps

目的：把进程内存dump下来（类似二进制可执行文件的解压缩表示）之后是否已经`unpacked & deobsfucated`.

## On the Reverse Engineering of the Citadel Botnet 

Citadel是一种先进的信息窃取恶意软件，针对财务信息。由于复杂的结构和先进的反逆向工程技术，Citadel恶意软件分析过程既具有挑战性又耗时。

该论文提供了两种类型的代码分析技术，即汇编到源代码匹配和二进制克隆检测。

论文是静态地去除混淆，动态地监控程序行为

