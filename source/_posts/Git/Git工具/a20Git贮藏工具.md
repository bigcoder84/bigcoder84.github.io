---
title: 二十.Git贮藏工具
toc: true
categories:
  - Git
  - Git工具
tags:
  - Git
  - Git工具
abbrlink: 26141
date: 2019-07-13 19:50:36
---

有时，当你在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态，而这时你想要切换到另一个分支做一点别的事情。 问题是，你不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。 针对这个问题的答案是 `git stash` 命令。<!--more-->

​	`git stash`命令相当于将工作区中的工作树状态保存到一个堆栈中，此时我们就可以切换到另一个分支去进行其他工作了。

## **将当前工作区状态存入栈中**

```shell
git stash 
```

## **查看堆栈中保存的状态**

```shell
git stash list
```

## **将堆栈中的状态应用到工作区中**（不删除栈中保存的状态）

```shell
git stash apply <贮藏名>
```

​	如果想要应用其中一个更旧的储藏，可以通过名字指定它，像这样：`git stash apply stash@{2}`。 如果不指定一个储藏，Git 认为指定的是最近的储藏：

## **弹栈操作**

​	与`apply`不同的是，它将状态应用到工作区后，会删除栈顶元素。

```shell
git stash pop
```

## **删除栈中保存的某个状态**

```shell
git stash drop <贮藏名>
```

