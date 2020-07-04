---
layout: post
title:  "Summary-REDQUEEN"
date:   2019-04-10 9:55:01 +0800
categories: 基于动态的恶意软件分析
tag: 论文调研
---
* content
{:toc}


# REDQUEEN(NDSS'19)

---

## 问题

Fuzzing的`Magic Bytes`和`Checksum`问题。

## 贡献

- 本文引入了一种输入到状态的相关性原则，来加速反馈驱动的fuzzing;
- 利用输入和程序状态的相关性，替代符号执行和污点追踪，来解决fuzzing过程中的magic byte和checksum问题;
- 实现REDQUEEN原型系统。综合评估结果表明，REDQUEEN在几个指标中优于所有最先进的模糊测试工具。

## 原理

在许多情况下，输入的一部分在运行时直接对应于存储器或寄存器。因此，**输入和当前程序状态之间存在强大的输入到状态对应关系（Input-to-State Correspondence）。**

### *Magic Bytes*

1. `Tracing:`当fuzzing一个新的输入时，hook所有的cmp指令和call指令，并提取参数，得到自定义的`mutation<pattern ---> repl>`(这里pattern代表输入中的一部分，而repl代表`Magic Bytes`)。
2. `Variations:`因为无法知道比较后采用哪一个标志位（无法区别`>`或`=`），系统会根据被比较的值产生一个变体（例如将被比较的值加1或减1）。
3. `Encoding:`对自动义的mutation进行一些常见的编码，例如将整形数字编码为ASCII数字等。
4. `Application:`利用repl代替输入中的pattern部分。
5. `Colorization:`为每一个输入产生一个经过“着色”的版本，减少输入中可被repl替换的pattern位置。例如，输入为`ZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZ`，此时产生的mutation为`<zzzzzzzz ---> MAGICHDR>`,那么输入中被repl替换的位置就会有很多（24个）。将输入染色为`QYISLKFYDBYYSYWSIBSXEAXOKHNRUCYU`，则只会产生一个mutation，`<QYISLKFY→MAGICHDR>`。
6. `Strings and Memory：`除了上面提到的关于整数的比较，程序经常会调用函数去比较两个字符或字节数组，如果函数需要至少两个指针参数，系统会提取指针指向的前128个字节，然后他们按照整数的方式进行处理。对于内存内容，系统要么仅仅比较前n（n从4到32不等）字节`memcmp()`，要么比较直到null的所有字节`strcmp()`。
7. `Input Specific Dictionary:`将许多连续的非0或非0xff的值加入到一个特别的字典中，用于输入的突变阶段。

### *Checksum*

1. `Identification:`在处理Magic Bytes的Step 1中，会产生一系列的比较指令，利用一些启发式方式找到那些可能和checksum相关的比较指令。
2. `Patching:`将Step 1中那些可能与checksum相关的指令替换为比较成功的指令，然后继续fuzzing。这有可能会移除一些边界检查等条件，所以会在Step 3中进行进一步处理。
3. `Verification:`将Step 2中存留的input应用到原生的二进制程序中看是否会产生新的路径，将那些依旧能产生新路径的输入保留下来。

## 优缺点

优：相对于符号执行和污点追踪，性能开销更小，更轻量

缺：无法处理PNG文件格式、对于base64编码不支持



## 启发

* 解决条件约束不一定非得依靠符号执行，也可以尝试先用本文类似的方式来解决
