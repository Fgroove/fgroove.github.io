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

* `tip.pgd/tip.pge`:没啥用，跳转地址在fup里面。