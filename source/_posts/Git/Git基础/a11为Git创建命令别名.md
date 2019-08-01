---
title: 十一.为Git创建命令别名
toc: true
categories:
  - Git
  - Git基础
tags:
  - Git
abbrlink: 16523
date: 2019-07-07 10:05:17
---

## **前言**

在Git系统中，有些命令需要和部分参数或选项连用才能达到效果，但过长的参数列表输入会严重影响我们的工作效率，幸运的是Git提供了和Linux系统一样的命令别名功能，这样我们就可以通过别名来调用常用命令了。<!--more-->

## **定义别名**

```shell
git config --global alias.自定义别名 命令 #--global是别名的作用域，通常有local、global、system
```

例如：我们需要使用`st`代替`status`

```shell
git config --global alias.st status
```

​	此时`git st`与`git status`等价。

听到微笑自己比较喜欢创建一个`log --all --graph`的别名：

```shell
git config --global alias.logg 'log --all --graph'
```



