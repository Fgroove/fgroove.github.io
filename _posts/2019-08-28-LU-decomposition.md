---
 Javalayout: post
title:   "LU分解"
date:   2019-08-28 16:55:01 +0800
categories: "Job-Hunting"
tag: "interview"

---

* content
{:toc}




[对在日交流期间课程的整理]()

# Lecture10.LU分解法

## 大纲

1. LU分解法的概述
   * 高斯消元法
   * 

2. 



## LU分解法概述

$Ax=b$, $A$是密集矩阵，$x、b$是向量，求解向量$x$。解法有

* 直接解法：通过矩阵操作求出准确解。
* 反复解法：反复求解近似值使其逼近准确解。

### 高斯消元法

n个方程n个未知数，消元法可解。

### Gauss-Jordan 消元法

利用单位矩阵`I`求出A的逆矩阵。$(A | I)->(I | A^{-1})$

每一步计算量相当，并行化是负载平衡性好。

