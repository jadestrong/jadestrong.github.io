---
title: 备份Blog源码--git学习
date: 2016-09-12 18:30:30
tags:
---

> 在使用Hexo进行博客部署时，仅将生成的静态博客文件上传到了github中，通过GitHub Page进行展示，但是项目的源码都是存储到本地的，所以这样做不是很安全，完全有可能发生源码丢失这种问题，所以在这里建立github分支来存储源码。

命令如下：

```
git init  // 默认这个文件夹是没有进行git初始化的

git remote add origin {your-git-repo-url}  //这个是添加一个新的远程仓库
git checkout -b source  //这是检出一个分支么？
git push origin source  //这个是将谁推给谁呢，目前看可能是origin -> source？

```

在这里曾出现一个问题：`error: src refspec {branch name,如source} does not match any`:
> 这个是因为推送的目录中没有文件，应该先将当前目录的文件都添加上去：

```
git add .
git commit -m 'some thing'
git push origin source //即可成功
```
