---
layout: post
title:   "数据处理-IDAPython"
date:   2019-12-31 08:55:01 +0800
categories: 基于动态的恶意软件分析
tag: 数据处理
---

* content
{:toc}






# IDA Python

## idaapi

* `askaddr`

```python
tgtEA = idaapi.askaddr(0, "Enter target address")
if tgtEA is None:
  exit
```





## IDAUTIL

| API                                   | 说明                                                    | 参数                                                         | 返回值                           |
| ------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------ | -------------------------------- |
| refs(ea, funcfirst, funcnext)         | 通用参考收集器，仅供内部使用                            |                                                              |                                  |
| CodeRefsTo(ea, flow)                  | 获取对“ ea”的代码引用列表                               | ea:目标地址<br />flow:是否遵循正常的代码流程,布尔值          | 引用列表（可能为空）             |
| CodeRefsFrom(ea, flow)                | 从“ ea”获取代码引用列表                                 | 同上                                                         | 同上                             |
| DataRefsTo(ea)                        | 获取对“ ea”的数据引用列表                               | +1                                                           | +1                               |
| XrefTypeName(typecode)                | 将交叉引用类型代码转换为可读名称                        | typecode：交叉引用类型代码                                   |                                  |
| _copy_xref(xref)                      | 制作外部参照类的私有副本以保留其内容                    |                                                              |                                  |
| XrefsFrom(ea, flags=0)                | 返回地址“ ea”中的所有引用                               |                                                              |                                  |
| XrefsTo(ea, flags=0)                  |                                                         |                                                              |                                  |
| Threads()                             | 返回当前调试对象的所有线程ID                            |                                                              |                                  |
| Heads(start=None, end=None)           | 获取列表（指令或数据）                                  | start：起始地址（默认值：inf.min_ea）<br />end：结束地址（默认值：inf.max_ea） |                                  |
| Functions(start=None, end=None)       | 获取函数列表                                            | 同上                                                         |                                  |
| Chunks(start)                         | 获取函数块列表                                          |                                                              |                                  |
| Modules()                             | 返回具有名称，大小，基础和rebase_to属性的模块对象的列表 |                                                              |                                  |
| Names()                               | 返回名称列表                                            |                                                              | 元组列表（ea，name)              |
| Segments()                            | 获取二进制文件中的段（节）列表                          |                                                              | 段起始地址列表                   |
| Entries()                             | 返回入口点列表（出口exports）                           |                                                              | 元组列表（索引，序数，ea，name） |
| FuncItems(start)                      | 获取函数项列表（函数边界内的指令或数据项）              | start：函数的地址                                            | 函数中每个项目的ea               |
| Structs()                             | 获取结构列表                                            |                                                              | 元组列表（idx，sid，名称）       |
| StructMembers（sid）                  | 获取结构成员信息的列表（如果给定框架，则获取堆栈变量）  | sid：结构的ID                                                | 元组列表（偏移量，名称，大小）   |
| DecodePrecedingInstruction(ea)        | 解码执行流程中的前一条指令。                            | ea：解码地址                                                 |                                  |
| DecodePreviousInstruction(ea)         | 解码前一条指令并返回类似insn_t的类                      |                                                              |                                  |
| DecodeInstruction(ea)                 | 解码指令并返回类似insn_t的类                            |                                                              |                                  |
| GetDataList(ea, count, itemsize=1)    | 获取数据列表，仅供内部使用                              |                                                              |                                  |
| PutDataList(ea, datalist, itemsize=1) | 放置数据列表，仅供内部使用                              |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |
|                                       |                                                         |                                                              |                                  |

