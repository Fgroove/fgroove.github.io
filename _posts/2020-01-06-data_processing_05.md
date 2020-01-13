---
Javalayout: post
title:   "数据处理-IDAPython"
date:   2020-01-06 08:55:01 +0800
categories: "HWProject"
tag: "malwareAnalysis"

---

* content
{:toc}






# IDA

反汇编文件中包含不是函数的部分，既无法通过`get_func()`获取函数信息，但是执行到的基本块。S2E的高亮代码没有解决这个问题，可能是已经处理了二进制文件或者就是存在该问题。

* S2E记录了执行的基本块，直接高亮标记即可
* pinlog记录了基本块的偏移地址

## 代码逻辑

* fup/tip地址全部高亮
* 高亮代码块里面call指令跳转代码块全部高亮
* 基本块最后一行代码，succs()函数和tnt决定跳转

* 所有基本块中，未被标色的，使用preHead查看是否有系统调用
