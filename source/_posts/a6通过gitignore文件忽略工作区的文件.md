---
title: 六.通过gitignore文件忽略工作区的文件
categories:
  - Git
  - Git基础
tags:
  - Git
abbrlink: 54773
date: 2019-07-05 19:42:53
---

​		一般我们总会有些文件无需纳入 Git 的管理，也**不希望它们总出现在未跟踪文件列表**。  在这种情况下，我们可以创建一个名为 `.gitignore` 的文件（在`.git`文件夹同名的目录中创建），列出要忽略的文件模式。 <!--more-->来看一个实际的例子：

```shell
$ cat .gitignore
*.[oa]
*~
```

​		第一行告诉 Git 忽略所有以 `.o` 或 `.a` 结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的。 

​		第二行告诉 Git 忽略所有以波浪符（~）结尾的文件，许多文本编辑软件（比如 Emacs）都用这样的文件名保存副本。 此外，你可能还需要忽略 log，tmp 或者 pid 目录，以及自动生成的文档等等。 要养成一开始就设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。

文件 `.gitignore` 的格式规范如下：

- 所有空行或者以 `＃` 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配。
- 匹配模式可以以（`/`）开头防止递归（即使我们在该文件中写“src/”这样的路径，也会将所有包含这一名字的目录全部排出，例如：demo/src/也会被忽略，但如果我们写的是“/src/”，那么“demo/src/”不会被排除）。
- 匹配模式可以以（`/`）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（`!`）取反。

​		所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]`匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（`*`) 表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` , `a/b/z` 或 `a/b/c/z` 等。

​		**`.gitignore`文件可以存放在工作目录任意子目录中，这样一来该文件只能影响该目录下的文件和路径。**例如：我们在src下创建一个`.gitignore`文件，那么此时在这个文件中写`*.class`，表示`src/*.class`自动忽略。

我们再看一个 .gitignore 文件的例子：

```shell
# no .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in the build/ directory
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
```



**注意**：添加`.gitignore`的目的就是避免我们在使用`git status`命令时，部分文件总是出现在“未跟踪列表”中。这个文件并不是解除Git对这些文件的监控，如果一个文件已经被Git跟踪，是不能通过`.gitignore`文件解除跟踪的。