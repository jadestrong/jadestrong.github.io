---
title: Git保证fork的项目和原项目保持同步
date: 2017-02-01 10:29:29
tags:
---

问题描述：当我们在github上fork出一个项目后，如果原有的项目更新了，怎样保持我们fork出的项目可以和原项目保持同步呢？
1. 在Fork的代码库中添加上游代码库的remote源，该操作只需要执行一次，`git remote add upstream https://github.com/jadestrong/spacemacs-private.git`;
2. 将本地的修改commit；
3. 在每次`pull request`前做如下操作，即可以实现和上游版本库的同步。
 3.1：`git remote update upstream`；
 3.2：`git rebase upstream/{branch name}`;
需要注意的是在操作3.2之前，一定要将checkout到{branch name}到指定的branch，`git checkout develop`。
4. Push代码到Github `git push`。
