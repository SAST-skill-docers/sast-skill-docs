# Git：分布式版本控制软件

!!! note "什么是分布式版本控制软件"
	![image-20211007231126799](https://i.loli.net/2021/10/07/yTHKCmGkxJXUOSj.png)
	
	在代码开发过程中，总会遇到一些问题。代码不知道改了哪里不能运行了，想要回退怎么办？多个人同时想对代码进行修改（并行开发），开发完后怎么合并？显然，有一个能帮助我们解决这些问题的工具将极大的加快我们开发的速度。而这种工具，便是版本控制软件。
	
	版本控制软件帮助使用者找出...
	+ 不同版本之间的差异
	+ 谁做出了这个修改
	+ 什么时候做出了这个修改
	+ 做出修改的人给出的修改理由
	
	那么，什么是分布式的版本控制软件呢？为了了解清楚，我们需要区分**中央版本控制系统**和**分布式版本控制系统**的区别。
	
	中央版本控制系统必须同时存在服务端和客户端。当进行代码备份时，客户端会向服务端发出请求，并将此次修改的内容发送到服务器当中去，服务端收到请求后，会将代码存储在服务器当中；同样当客户端想查看某一个版本的修改内容或者想恢复到某一个版本之时，客户端也会发送请求到服务端，服务在端接收到请求之后会做出相应的处理并返回给客户端。也就是说，在业务流程过程中，服务端必须存在，**所有请求必须经过服务端的处理**。
	
	分布式版本控制器，主要是将备份的代码以及记录**完全独立在本地存储**。比如说当你想将代码恢复到某一个版本的时候，本地版本控制器**不需要依赖网络**便可以完成此操作，因为本地版本控制器拥有**完整独立的一套控制系统**。
	
	Excerpted from https://www.imooc.com/read/51/article/1008.

!!! note "什么是 Git"
	Git 是 Linux（一种程序员热爱的操作系统） 之父 Linus Torvalds 为开发 Linux 内核而建立的一个**分布式版本控制软件**。
	
	Git 除了版本控制软件本身的优势以外，还可以...
	+ 通过查看 `git history`，开发者可以看到一个项目开发的时间线
	+ 通过 `git branch` （分支），开发者可以在不用担心影响主代码的情况下进行开发

## 前置知识
+ 命令行命令的使用操作
+ 愿意**动手实践**的决心（多尝试）
+ 做好笔记整理与总结的能力（遗忘后便于快速回忆）

## 从安装与配置开始

### Windows
#### 编辑器
Git 需要绑定文本编辑器使用，自带支持以下编辑器：
+ Nano
+ Vim
+ Notepad++
+ VS Code
+ Sublime
+ Atom
+ VS Codium

如果你没有安装上述文本编辑器，那么你使用的很可能是⻛评并不好的记事本(Notepad)，不建议拿它作为git的绑定编辑器。这种情况下建议使用Notepad++，它的操作与界面可以和记事本实现很好的过渡。为了方便下载我将它传到了科协云盘，下载链接为：https://cloud.tsinghua.edu.cn/f/9bd487642fcb4e468c67/?dl=1，安装过程中全部点“下一步”（或同位置的按钮）即可。完成之后你将和记事本永远告别，打开文本文件的默认方式会变成 Notepad++。

#### 下载
安装包下载地址：https://gitforwindows.org/
国内的镜像：https://npm.taobao.org/mirrors/git-for-windows/
清华镜像（校内访问较快）：https://mirrors.tuna.tsinghua.edu.cn/github-release/git-for-windows/git/

#### 安装
下面内容英文大佬或者熟练掌握了 `git config` 指令（乃至会直接修改配置文件）的同学可以忽略在安装过程中大部分选项，直接点“Next”即可。但是有两个地方需要注意：编辑器和换行符。

编辑器默认选项是 Vim，如果没有 Vim 经验的同学，请选择其他熟悉的编辑器（如上面提到的
NotePad++）。

![image-20211008145023695](https://i.loli.net/2021/10/08/3P62Ayk9hUEVw5t.png)

对于换行符，在 Windows 中，默认的换行是 CRLF，但实际开发时，通常会发现大家一致使用 LF，因此我们选择第二项，并希望大家开发时保持 LF的习惯。

![image-20211008145130391](https://i.loli.net/2021/10/08/FHmSajyeDAnI2Cd.png)

### Debian/Ubuntu

```bash
apt-get install libcurl4-gnutls-dev libexpat1-dev gettext 
apt-get install git
```

### Centos/RedHat

```bash
yum install curl-devel expat-devel gettext-devel 
yum install git-core
```


### Mac
一般 Mac 平台是自带 Git 的。

如果实在没有在 Mac 平台上安装 Git 最容易的当属使用图形化的 Git 安装工具，下载地址为：
http://sourceforge.net/projects/git-osx-installer/

有 homebrew 的同学也可以用 `brew install git` 安装（或者自学怎么安装 homebrew）

### 配置 Git

使用如下命令可以配置自己在 git 中的用户名和邮箱，你也可以通过 `git config` 命令按自己的喜好配置更多东西。

```bash
git config --global user.name <name>
git config --global user.email <email-address>
```

## 基础操作
+ Repo(sitory) 简介

包含了一个项目的所有文件、文件夹，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。（可以理解成代码的文件根目录）

+ 创建仓库

创建一个新文件夹作为你的第一个 Repo，在命令行中进入该文件夹，输入 `git init`，以使用 git 来管理这个文件夹。（初始化 Repo 仓库）

![image-20211008145824725](https://i.loli.net/2021/10/08/EJRlQmMG3CgHaPh.png)

+ 添加文件

在这个文件夹中添加文件（例如我们写了一个 test.py），使用 `git add test.py` 将文件到目前为止的修改放入 git 的暂存区。

![image-20211008145833054](https://i.loli.net/2021/10/08/3onX9gIdY57cEMW.png)

一次性添加工作环境目录下的所有文件，使用 `git add .`

+ 记录修改

当所有的修改都用 `git add` 加入到暂存区后，就使用 `git commit –m "在这里输入你这次版本迭代都干了什么，注意是半角引号"` 将所有的暂存区里的修改提交至本地仓库。

![image-20211008145902465](https://i.loli.net/2021/10/08/GeqgAYIbx4QWmSr.png)

+ 版本迭代

为了展示 git 的作用，我们在 test.py 中随便输入了一些内容。

然后再次执行 `git add test.py`, `git commit –m "…"` 提交更改。

![image-20211008145952669](https://i.loli.net/2021/10/08/R3wUTtaAZHjSxEV.png)

+ 查看历史

通过 `git log`，我们可以查看之前的 commit 记录，以及对应的 sha 编码。

![image-20211008150040805](https://i.loli.net/2021/10/08/4mn5Jf9SBUlbpVF.png)

+ 版本回退

首先，我们必须指定要回退到的版本。而指定版本有两种方式。

(1) 相对寻址。在 Git 中，用`HEAD`表示当前版本，也就是最新的提交`1aada331...`（注意不同工程，不同次暂存(Commit)的版本 ID 肯定不同），上一个版本就是 `HEAD^`，上上一个版本就是 `HEAD^^`，当然往上 100 个版本写 100 个 `^` 比较容易数不过来，所以写成 `HEAD~100`。

(2) ID 寻址。如上述 "modify test.py" 这个版本，可以用版本 SHA ID 的前几个字符来表示（只要没有歧义即可），比如 c9df5e。

而了解了怎样指定版本后，我们便可以使用 `git reset --hard <Version>` 来恢复到之前的版本了。

+ 工作区，暂存区与分支的概念

工作区(Working Directory)指你电脑上存储的当前项目文件（最新），版本库（Repository）中存了很多东西，其中最重要的就是暂存区（Index），还有 Git 为我们自动创建的第一个分支（Branch）`master`，以及指向`master`的一个指针叫`HEAD`。

![image-20211008184709509](https://i.loli.net/2021/10/08/VZXzSgrhc8yFx75.png)

`git add` 命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行 `git commit` 就可以一次性把暂存区的所有修改提交到分支。

## 同步开发

### 分支的简介

分支(Branching)就是科幻电影里面的平行宇宙，当你正在电脑前努力学习Git的时候，另一个你正在另一个平行宇宙里努力学习SVN。如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了Git又学会了SVN！

![learn-branches](https://www.liaoxuefeng.com/files/attachments/919021987875136/0)

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

附：分支的介绍 https://www.liaoxuefeng.com/wiki/896043488029600/896954848507552


## 远程 Git 仓库

显然，大家不会都挤在你的电脑上开发。我们记得，Git 是分布式的版本控制软件。于是，我们需要一种方式进行同步。例如，把 Git 仓库放在网上。

在这里我们介绍几种远程的 Git 仓库：

+ GitHub：<s>全球最大同性交流网站</s>一个基于Git的代码托管服务平台，开源社区交流代码的重要网站https://www.github.com/
+ GitLab：类似Github，但主要面向企业、组织等内部合作 https://www.gitlab.com/
+ Tsinghua Git：清华大学基于 GitLab 搭建的大学内部的 Git，适用于课程小组内部或者学生组织内部的合作 https://git.tsinghua.edu.cn/

下面我们介绍一些在远程仓库控制时的基本操作：

+ 克隆仓库到本地

比如，以[本技能引导文档](https://github.com/SAST-skill-docers/sast-skill-docs)所在的 Git 仓库为例。我们使用 `git clone git@github.com:SAST-skill-docers/sast-skill-docs.git `，这样便把远程仓库中的内容取到了本地，并创建了工作区。

![image-20211008200605468](https://i.loli.net/2021/10/08/6sKx2NSBD98pcWE.png)

![image-20211008200810881](https://i.loli.net/2021/10/08/EOyfHRC97DTQnk6.png)

+ 添加远程仓库地址

有时，我们的项目是使用 `git init` 来创建的，并不是从 GitHub 上直接 clone 的别人的代码。这时我们要首先在 GitHub/GitLab/Tsinghua Git 上新建一个 Repo，然后按照提示，使用 `git remote add origin <你的 Repo 的 HTTP/SSH 地址>`。这样便是告诉本地的 Git 管理器，这个地址起一个简便的名字叫做 `origin`，方便今后使用。

+ 推送更改



+ 拉取更改



## 后续拓展

想要学习更多 Git 内容？

+ `git help`
+ Pro Git Book https://git-scm.com/book/zh/v2
+ 学习 Git 分支 https://learngitbranching.js.org/?locale=zh_CN



## 参考资料

+ 2021 暑培讲义 by tshoigyr
+ 2020 暑培讲义 by rls
+ 2021 春季学期《面向对象程序设计基础》课程讲义
+ 廖雪峰的 Git 教程 https://www.liaoxuefeng.com/wiki/896043488029600