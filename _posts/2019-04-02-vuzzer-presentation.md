---
layout: post
title:  "vuzzer-presentation"
date:   2019-04-03 8:55:01 +0800
categories: 基于动态的恶意软件分析
tag: 反沙箱对抗技术
---
* content
{:toc}


# VUzzer: Application-aware Evolutionary Fuzzing

(NDSS'17)

---

## Page.13

Static analysis: CMP Immediates, Rank basic blocks to figure out which basic part are hard to reach.

First round: 

* Seed Inputs(Known valid); 
* enable dynamic Taint analysis to give us interesting things for example error-handling code, magic bytes, lea offset
* Apply fitness function on the code as being executed and then we end up with scoring each input. Inputs that execute a high rank basic block get high socres and are more likely to be used in the next round.
* disable Dynamic taint analysis and only enable it when new code is covered.