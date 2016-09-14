---
title: Git的基本指令
date: 2016-09-14 20:51:43
tags: git 
---

## Git的基本指令

当我们在本地拥有多个分支时，需要知道我们当前是在哪一个分支，此时使用命令：
```
git branch -a
//输出如下
* source
  remotes/origin/master
  remotes/origin/source
```
上面的输出显示，在远程端拥有两个分支，其一是master，这个是我们在创建Repo时生成的；
之后，我们又在远程端创建了一个source分支，在本例中是存放的Blog项目的源码，而master分支中则是我们博客的静态文件。

所以，当我们创建了新的博客并部署之后，其实是将文件部署到了master的分支中，而对源码的备份则需要将代码push到source分支。
