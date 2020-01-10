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

反汇编文件中包含不是函数的部分，既无法通过`get_func()`获取函数信息，但是执行到的基本块。



## 代码逻辑

* `tip.pgd/tip.pge`:没啥用，跳转地址在fup里面。