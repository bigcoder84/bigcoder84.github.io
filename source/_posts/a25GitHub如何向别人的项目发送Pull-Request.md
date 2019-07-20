---
title: GitHub如何向别人的项目发送Pull Request请求
thumbnail: /gallery/thumbnails/a25.png
toc: true
categories:
  - Git
  - Github
tags:
  - PullRequest
abbrlink: 17497
date: 2019-07-20 16:02:48
---

Pull Request 是社会化编程的象征。GitHub 创造的这一功能，可说给开源开发世界带来了一场革命。不会用这个功能，就等于不会用GitHub。不过，刚刚接触 GitHub 的人在发送 `Pull Request` 时，往往会遇到找不到对方的项目或者不知道该如何发送等问题。<!--more-->

本文就将详细讲述`Pull Request`的全过程，并为大家提供一个练习`Pull Request`的机会。

## **到底什么是Pull Request**

 很多新手小白在使用GitHub时经常看到这个词，但对这个词的真正含义并不理解。`Pull Request`实际上就是自己修改别人项目的源代码后，请求对方仓库采纳该修改时采取的一种行为。所有对仓库有读权限的用户都能发送`Pull Request`。

例如，我们使用GitHub上面托管的开源软件后，发现了其中的某个BUG，我们通过自己的努力解决了这个BUG（修改源码），此时我们可以向该仓库发送Pull Request，如果该修改被仓库管理员采纳，那么别人在使用该开源软件就不会出现该BUG了。



## **发送Pull Request的具体步骤**

###  **第一步：进入任意仓库页面**

 这里博主为大家提供一个现成的测试仓库 [https://github.com/HearingSmile/TestPullRequest](https://github.com/HearingSmile/TestPullRequest)大家可以按照文章所讲解的步骤，向这个仓库提交自己的Pull Request，练习Pull Request具体操作。



### **第二步：Fork该仓库**

{% asset_img 1.png %}

点击图片中右上角的Fork按钮，将TestPullRequest仓库Fork到自己账户中：

{% asset_img 2.png %}



### **第三步：clone仓库**

我们需要将自己账户中的TestPullRequest仓库克隆到本地。

我进入系统中任意目录下，执行下列命令完成克隆操作：

```shell
git clone https://github.com/xxx/TestPullRequest.git  #URL获取，见下图
```

{% asset_img 3.gif %}



### **第四步：新建分支，在分支上完成修改**

当前 Git 的主流开发模式都会使用特性分支，所以我们修改之前需要创建一个分支，然后在master分支上执行这次修改。

**在创建分支之前我们可以先确定一下远程分支：**

```shell
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

克隆后的仓库有一个master本地分支，但是我们最好不要在主分支上进行修改，我们需要创建一个特性分支：

**创建分支：**

 ```shell
git checkout -b work origin/master
 ```

我们特性分支的名字叫做work，它跟踪远程分支origin/master。



**再次确认当前仓库的分支状况：**

 ```shell
$ git branch -avv
  master                e31200d [origin/master] Init Reporsitory
* work                  e31200d [origin/master] Init Reporsitory
  remotes/origin/HEAD   -> origin/master
  remotes/origin/master e31200d Init Reporsitory
 ```



### **第五步：修改代码**

{% asset_img 4.png %}

我添加14行中的代码



### **第六步：提交更新，并Push到GitHub上**

**用diff命令查看修改是否成功：**

```shell
$ git diff index.html
diff --git a/index.html b/index.html
index 2fc45aa..7088d2a 100644
--- a/index.html
+++ b/index.html
@@ -11,5 +11,6 @@
 </head>
 <body id="page-index">
        <p>1.第一次提交</p>
+       <p>2.Pull Request测试</p>
 </body>
 </html>
\ No newline at end of file
```

**提交修改，并push到GitHub上去：**

```shell
$ git add index.html
$ git commit -m 'Update index.html'
$ git push origin work   #work表示push到远端的work分支上去，如果远端没有则自动创建远端分支
```



### **第七步：发送Pull Request**

**切换到work分支：**

{% asset_img 5.png %}

**点击`New pull request`按钮：**

{% asset_img 6.png %}

**发送Pull Request：**

{% asset_img 7.png %}

**提交成功：**

{% asset_img 8.png %}