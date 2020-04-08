---
Javalayout: post
title:   "Sublime Text Python开发环境"
date:   2019-12-02 08:55:01 +0800
categories: "HWProject"
tag: "malwareAnalysis"

---

* content
{:toc}






## Sublime Text Python开发环境

### macOS

macOS安装Sublime Text之后，Terminal没有默认启用`subl`命令。

```shell
sudo ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl
```

macOS的SIP(System Integrity Protection)机制，使得即使`sudo`也不能修改`/usr/bin`, 所以用`/usr/local/bin`.

`Tools -> Build System -> Python` ,`CTRL + B`运行。

### Ubuntu

`Tools -> Build System -> Python` ,`CTRL + B`运行。

## 数据处理

virus total报告`json`文件数据处理，

```python
import json

with open('report.json') as f:
  data = json.load(f)
  
with open('new_report.json', 'w') as f:
  json.dump(data, f, indent=2)
```

`sort_keys:` 是否按照字典顺序`a-z`输出，`True/False`；

`indent=4:` 设置缩进格数；

`separators:` 设置分隔符



syscall数据提取,

```python
import json

with open('report.json') as f:
  data = json.load(f)
  
for key in data['event']:
  name = key[name]
  
with open('new_report.json', 'w') as f:
  json.dump(data, f, indent=2)
```

利用`字典不允许重复`的特性去重，

```python
unique = {each['name']: each for each in te}.values()
```

