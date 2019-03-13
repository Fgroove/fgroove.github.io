---
layout: post
title:  "BitBucket"
date:   2019-03-13 14:55:01 +0800
categories: 杂记
tag: Tools
---
* content
{:toc}


# Bitbucket
---
## 初始化
见[教程](https://www.jianshu.com/p/08b7048ec925)。

## 常用命令
把当前目录变成Git可以管理的仓库 , 生成的`**.git`隐藏文件不能修改.

```shell
git init
```

关联远程仓库

```shell
git remote add origin 项目地址
```

把本地仓库推送到远程仓库 , 即推送到Bitbucket创建的仓库

```shell
git push –u origin master
```
第一次要用`-u`以后不需要

---
别人的项目
```shell
git clone ....git
```
假如需要修改文件或者添加文件 , 那么就重复`git add –A`提交文件到暂存区→ `git commit –m“注释”`把暂存区的文件提交到本地仓库→`git push`推送到远程仓库.

更新`git pull` 