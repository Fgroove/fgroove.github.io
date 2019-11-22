---
Javalayout: post
title:   "Bitbucket with Git"
date:   2019-11-21 08:55:01 +0800
categories: "HWProject"
tag: "nitro"

---

* content
{:toc}




#[使用Git](https://juejin.im/post/5c2ecd3e6fb9a049ef26b6c4)处理Bitbucket

## 1.设置SSH key

```shell
ssh-keygen
```

将公钥加到bitbucket账户的设置中，不要加到你自己的仓库设置中，不然会出现授权问题。

```shell
cat ~/.ssh/id_rsa.pub
```



## 2.添加仓库和文件

* 建立文件夹，`git clone`项目
* 添加文件
* `git add/commit/push`