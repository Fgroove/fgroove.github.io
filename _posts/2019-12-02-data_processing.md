---
Javalayout: post
title:   "数据处理"
date:   2019-12-02 08:55:01 +0800
categories: "HWProject"
tag: "malwareAnalysis"

---

* content
{:toc}






# 数据处理

virus total报告`json`文件数据处理，

```python
#encoding:utf-8
import json

dic = {'a': 1, 'b': 2, 'c': 3}

js = json.dumps(dic, sort_keys=True, indent=4, separators=(',', ':'))
print(js)
```

`sort_keys:` 是否按照字典顺序`a-z`输出，`True/False`；

`indent=4:` 设置缩进格数；

`separators:` 设置分隔符



