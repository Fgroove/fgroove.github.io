---
layout: post
title:  "Preconditioned Symbolic Execution"
date:   2019-02-28 09:00:01 +0800
categories: 基于动态的恶意软件分析
tag: 反沙箱对抗技术
---

* content
{:toc}


# 2019-02-28-Weakest Preconditions

## Traditional WP

*I. Jager and D. Brumley. Efficient directionless weakest preconditions. Technical Report CMU-CyLab-10-002, Carnegie Mellon University, CyLab, Feb. 2010.*

Program `P`和Post-condition `Q`的Weakest Precondition `wp`是布尔谓词，使得当`wp(P,Q)`成立时，保证执行`P`在满足`Q`的状态下终止。“最弱”是指对于所有其他的谓词`R`导致`P`终止于满足`Q`的状态，我们有`R⇒wp(P,Q)`。

Weakest preconditions是后向，自动，语法驱动的计算。因此，给定GCL程序，我们可以自动计算wp。给定语句i处的后置条件Q，我们计算出执行i的前提条件，以便满足Q.



## 举个例子

假设我们有一种语言，其中所有值和变量都是32位无符号整数，以及下面的程序，其中输入x：

```C
if (x%2 = 1) { s := x+2; }
	else { s := x+3; }
```

该程序的GCL表达为：

```c
(assume x%2 = 1; s := x + 2)􏰁 
(assume ¬(x%2 = 1); s := x + 3)
```

令Q为: s是算术溢出的结果的条件（即，`Q =(s<x)`，因为如果s不溢出并且环绕，则s将大于x）。 使用下表中的规则自动计算最弱的前提条件：

|    S     |       wp(S,Q)       |        wp(S,T)        |
| :------: | :-----------------: | :-------------------: |
|  x := e  |       Q[e/x]        |           T           |
| assert e |         e∧Q         |           e           |
| assume e |        e ⇒ Q        |           T           |
|  S1; S2  | wp(S1 , wp(S2 , Q)) |  wp(S1 , wp(S2 , T))  |
|  S1  S2  |  wp(S1,Q)∧wp(S2,Q)  | wp(S1, T) ∧ wp(S2, T) |

```c
x%2 = 1 ⇒ ((x + 2) < x) ∧ ¬(x%2 = 1) ⇒ ((x + 3) < x)
```

这个谓词只能通过赋值给x来导致溢出来满足。 如果没有这样的赋值（即谓词不可满足），那么我们保证程序将始终以不发生溢出的状态终止。

