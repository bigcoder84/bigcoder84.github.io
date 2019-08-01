---
title: 十九.禁用push-f操作
toc: true
categories:
  - Git
  - Git远程仓库
tags:
  - Git
  - Git远程仓库
abbrlink: 62026
date: 2019-07-13 19:26:59
---

通常情况下，我们都是禁用`push -f`操作的，因为它可能导致远端服务器的提交历史丢失。<!--more-->

{% asset_img 47.png %}

例如在远端此时的情况如上图所示，如果我们使用`git reset --hard 0b743`使得本地跟踪分支回退到历史的某个版本，也就是变成了这样：

{% asset_img 48.png %}

如果我们此时执行`git push -f <remote-name> <branch-name>` 进行强行推送的话，那么此时远端的master分支也会回退到`0b743`：

```shell
$ git push -f origin master
```

{% asset_img 49.png %}

