---
Javalayout: post
title:   "数据处理-IDA"
date:   2019-12-17 08:55:01 +0800
categories: 基于动态的恶意软件分析
tag: 数据处理

---

* content
{:toc}






## IDA Python

## 设置颜色

* ` BeginEA() `

```python
def get_new_color(current_color):
  colors = [0x00FF00, 0xFF0000]
  if current_color == 0xFFFFFF:
    return colors[0]
  if current_color in colors:
    pos = colors.index(current_color)
    if pos == len(colors)-1:
      return colors[pos]
    else:
      return colors[pos+1]
  return 0xFFFFFF
 
 
current_color = GetColor(addr, CIC_ITEM)
new_color = get_new_color(current_color)
SetColor(addr, CIC_ITEM, new_color) 
```



* `GetColor()`
* `SetColor():` 重复执行定深色
* 0xFFFFFF:白色未执行，0x00FF00：绿色执行一次， 0xFF0000：红色执行多次

### 去除颜色（设为白色）

```python
heads = Heads(SegStart(ScreenEA()), SegEnd(ScreenEA()))
for i in heads:
  SetColor(i, CIC_ITEM, 0xFFFFFF)
```

## 读入数据

```python
json_path = idc.AskFile(0, '*.an',
                            'Select the PT Information File')
```

* ` idc.AskFile() :`参数一，0代表读文件，1代表写文件。参数二，文件名。参数三，弹框信息。



