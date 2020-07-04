---
layout: post
title:  "AFL with Mac OSX"
date:   2019-03-07 09:05:01 +0800
categories: 基于动态的恶意软件分析
tag: AFL

---
* content
{:toc}


# AFL with OS X

---

## 308更新

之前Ubuntu出现的OOM Abort问题是因为用的是ConAFL（检测并发程序漏洞的改版AFL），导致速度低下，Out of Memory。

重新编译AFL之后可正常。ConAFL需要的资源明显多于AFL，CPU单项需求约是AFL的5倍以上。速度却差了一个量级，对libtiff测试来讲。

## 序言

AFL模糊测试在OS X上性能有些低下，因为处理`fork()`调用原因。

```shell
WARNING: Fuzzing on MacOS X is slow because of the unusually high overhead of fork() on this OS. Consider using Linux or *BSD. You can also use VirtualBox (virtualbox.org) to put AFL inside a Linux or *BSD VM.
```

## 编译（Instrmentation）

默认`afl-clang/afl-clang++`,如果安装GNU GCC的话，设置`AFL_CC/AFL_CXX`到编译器路径。

```shell
AFL_CC=/usr/.../afl-gcc ./configure
AFL_CXX=/usr/.../afl-g++ ./configure
```



## reference

[How to compile AFL's LLVM mode in OS X](https://reverse.put.as/2017/07/10/compiling-afl-osx-llvm-mode/)