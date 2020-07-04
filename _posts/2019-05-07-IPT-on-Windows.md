---
layout: post
title:  "IPT on Windows"
date:   2019-05-07 9:55:01 +0800
categories: 基于动态的恶意软件分析
tag: Intel PT
---
* content
{:toc}


# Windows上利用IPT查找漏洞

## Intel PT 性能

1.Low over-head (15% CPU perf hit for recording) 

2.Logs directly to physical memory, bypassing TLB and eliminating cache pollution 

3.Minimal log format takes little time to record 

- One bit per conditional branch 
- Only indirect branches log dest address 

## How it works

Different kinds of trace filtering: 

1. Current Privilege Level (CPL) – used to trace all of user or kernel 
2. PML4 Page Table – used to trace a single process 
3. Instruction Pointer – used to trace a particular slice of code (or module) 

Two types of output logging: 

1. Single Range 
2. Table of Physical Addresses 

## WinAFL IntelPT

https://github.com/intelpt

• Tracing is used very often in fuzzing and dynamic analysis
• Intel Processor Trace is a promising mechanism for hardware tracing
• Intel is dedicated to producing high performance trace features

TODO List: 

1.Implement thread context switch tracing in a reliable way 

- We attempted ETW but the API is asynchronous 
- New solution is to use APC

**2.Modify a Hypervisor to be able to use Intel PT inside a Guest VM** 

• Coming in 2017! 

3.Understand how to trace VMM, SMM code and test with SGX software 

##  PT virtualization

VM Tracing 

1. ●  System-wide VMX transitions(VM Entry and Exit tracing/profiling) 

2. ●  Host only and Guest only tracing 

3. ●  Track root and non-root VMX operations 

