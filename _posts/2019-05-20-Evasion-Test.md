---
layout: post
title:  "Evasion Test"
date:   2019-05-20 09:05:01 +0800
categories: 基于动态的恶意软件分析
tag: 逃逸检测

---
* content
{:toc}


# Evasion Test

---

## Evasive malware in RealWord

### Shamoon

* Shamoon: 时间触发，2012,2016,2017，节日期间攻击，沙特阿拉伯等中东地区
* StoneDrill：Shamoon 2.0，欧洲

* 恶意负载：泄露传播机密文件并抹除，破坏boot，使电脑不可用

### NetTraveler (aka TravNet) 和 PlugX RAT

* 用户点击触发

* 一旦被感染，ZeroT将与C&C链接，并上传感染的系统信息，然后下载多样化的PlugX RAT(remote access trojan)



## 测试框架

### Al-khaser

