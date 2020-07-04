---
Javalayout: post
title:   "Bitbucket with Git"
date:   2019-11-21 08:55:01 +0800
categories: 基于动态的恶意软件分析
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

关联仓库

```shell
git remote add origin git@github.com:YotrolZ/helloTest.git
```

```shell
$ git init // 初始化一个本地Git仓库(把本地的文件夹初始化成一个Git可以管理的版本库)
注意:需让命令行终端处在当前文件目录下
$ git add README.md // 把文件添加到本地版本库
$ git commit -m "first commit" // 把文件修改提交到仓库
$ git remote add origin git@github.com:your name/repository name.git // 将本地仓库和你自己的远程仓库关联起来，origin是仓库的名字
$ git pull origin master // 先将关联后的github仓库中的代码pull下来，
$ git push -u origin master // 将最新的修改推送到远程仓库 将本地仓库的文件推送到远程仓库，可能你的本地项目关联着几个仓库，
你可以根据仓库的名字git push origin master 将代码提交到不同仓库中，可以指定分支，
第一次使用加上了-u参数，是推送内容并关联分支。
推送成功后就可以看到远程和本地的内容一模一样，下次只要本地作了提交，就可以通过命令：
$ git push origin master 把最新内容推送到Github上关联的远程仓库中去。
```

