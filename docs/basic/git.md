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
+ （无特殊前置知识要求）
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



## 后续拓展

## 参考资料

+ 2021 暑培讲义 by tshoigyr
+ 2020 暑培讲义 by rls
+ 2021 春季学期《面向对象程序设计基础》课程讲义