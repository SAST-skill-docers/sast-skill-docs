# Linux

<center>
    <img src="https://i.loli.net/2021/10/10/NHZOsPCBmazyWjh.png" height="20%" width="20%">
</center>

## 前置知识

+ 有一台自己的电脑
+ 愿意**动手实践**的决心（多尝试）
+ 做好笔记整理与总结的能力（遗忘后便于快速回忆）

## 序言 Prologue

### What Is Linux?

Linux 是一种自由和开放源码的类 UNIX 操作系统，其内核由 Linus Torvalds 在 1991 年发布。

Linux 也是自由软件和开放源代码软件发展中最著名的例子，只要遵循 GNU，任何个人和机构都可以自由地使用 Linux 的所有底层源代码，这使得它得到来自全世界软件爱好者和组织的开发支持。

Linux 以各种形式被广泛应用在各个领域，包括但不限于：

+ 服务器、主机、超级计算机
+ 嵌入式系统（机顶盒、移动设备等）
+ 基础设施（红绿灯、工业传感器）

### Linux Distros

我们平时使用的“Linux”严格来说是 Linux 发行版本，而 Linux 狭义上单指操作系统的内核。

发行版本在内核的基础上还包括安装工具、系统配置、图形桌面界面、各种 GNU 软件等，使得这个系统能够适用于各种使用目的。

常见的 Linux 发行版有 Debian、Ubuntu、Fedora、CentOS、Arch Linux 等。不同的发行版使用的软件不同，有些发行版本是设计成专门的目的（比如 Kali 用于网安）。

我们一般使用的虚拟机的发行版为 Ubuntu 20.04。

### Ways to Use Linux

我们接触 Linux 的方式通常有以下几种：

+ 安装 Linux 系统（单系统、Windows & Linux 双系统）
+ WSL（英语：Windows Subsystem for Linux）**(Recommended)**
+ 虚拟机（Virtualbox、Docker）
+ 服务器远程连接（SSH、RDP）

我推荐有条件的同学给电脑装一个Linux系统。<s>这样你就能在各种配置中度过一段漫长而有趣的时光了</s>。

## Basic Concepts

+ **GUI**：Graphical User Interface. 图形用户界面，如其名。 
+ **CLI**：Command-Line Interface. 命令行界面，如其名。
+ **Shell**：一种软件，我们可以将它视为“用户到内核之间的中介”，它接受并解析（英语：Parse）用户输入命令行的指令，并调用内核所提供的对应服务。Shell 的发明是因为内核并不提供与用户交互的方式。

![OxQZi](https://i.loli.net/2021/10/10/9ESFQNB6ZKCYHm2.png)

其实 Shell 还分为图形界面和命令行界面两种，比如 Windows 的文件浏览器、程序管理器都可以算是 GUI Shell，但用 Linux 时我们一般说的都是 CLI Shell。

常见的 Shell 包括 sh、bash、zsh、fish 等。

+ **Terminal**：终端的名字来源于它在用户和机器交互过程中所处的位置：用户在终端输入、机器通过终端输出，现在我们已经不再需要专门的硬件充当终端，而是利用一个程序来模拟其行为。当我们打开一个终端模拟器（如 Windows 的 cmd，Linux 的 Konsole、gnome-terminal）时，一个 Shell 随即被运行，我们就可以通过这个 Shell 所提供的 CLI 输入指令了。

一个简单区分上述术语的回答：https://askubuntu.com/a/506880。

## Shell 101

打开终端，界面上会出现类似于下面样子的提示符：`training@SAST-Training:~$`。

从左到右依次是当前身份 `training`，主机名 `SAST-Training`，和当前工作目录 `~` 以及一个美元符号（fish 下是 `>` 号），这个美元符号表明当前用户不是 `root`。

如果你用 root 身份打开 Shell，呈现的提示符大概是这样 `root@SAST-Training:~#`。即 `#` 符号表明了 `root` 身份。

### How Does Shell Run Commands?

在 Shell 中你每次输入一个命令，这个命令被 Shell 解析、运行，并将结果输出到终端。

```bash
training@SAST:~$ echo "Hello SAST!"
Hello SAST!
```

Shell 的命令由空格分割，第一个是要运行的程序（如上例 `echo`），后面跟它的参数（如上例 `"Hello World!"`）。

注意到我们把想输出的 `Hello SAST!` 用引号扩了起来，这样本来空格分隔的两个词就合并成了一个参数。（其实这个例子中并不需要将它们合并，因为 `echo` 指令可以接受多个字符串输入。）

可是 Shell 怎么知道 `echo` 指的是什么呢？这些程序都是 Shell 内置的吗？其实当 Shell 遇到它不认识的程序关键字时，会查询环境变量 `$PATH`，在里面记录的路径中寻找 `echo` 这个程序，如果找到便执行。如果我们想知道这个程序实际所在的位置，可以使用 `which echo`。

```bash
training@SAST:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:
/usr/sbin:/usr/bin:/sbin:/bin
training@SAST:~$ which echo
/usr/bin/echo
```

所以当我们执行 `echo "Hello"` 时，实质上等价于执行 `/usr/bin/echo "Hello"`，后者明确指定了要执行程序的所在目录和它的名字。

### Basic Commands

接下来我们看一些基本指令。

|   Command       |  Description|
|:------------: |:------------------------:|
|  **目录相关**  | |
|  pwd      |          当前目录|
|     cd           |     切换目录|
|        ls            |  查看目录列表|
|   mkdir       |        创建目录|
|       find      |   在层级目录下搜索文件|
|    **文件相关**    | |
|      touch     |            创建|
|        mv     |     移动（可用于重命名）|
|        cp        |          拷贝|
|        rm        |          删除|
|      chmod    |         更改文件权限|
|      chown    |         更改文件所属|
|       echo      |      输出提供的文本|
|       file       |      查看文件类型|
|       cat    |    将文件内容输出到标准输出|
|  **用户和组相关**  | |
|     useradd     |         创建用户|
|     groupadd    |          创建组|
|      passwd      |        更改密码|
|     chpasswd   |        批量更改密码|
|        su       |     一般用于切换用户|
|       sudo   |    一般用于执行 root 权限指令|

其他一些可能用到的指令包括 `grep`、`ps`、`systemctl`、`ip` 等。

### File System

现在我们可以用 `ls` 和 `cd` 来翻阅系统里的文件了！

首先是一些基本的表示：`/` 表示根路径，`~` 表示家路径（`/home/username/`，如果是 root 则为 `/root/`），`.` 表示当前目录，`..` 表示上一级目录。这些表示省去了很多写绝对路径的麻烦。你可以试试 `cd` 到这些地方，然后用 `ls` 查看目录里的内容。

我们来看看根路径下都有哪些文件夹：

![Screenshot from 2021-07-11 17-20-03](https://i.loli.net/2021/10/10/SFkXU36brB2DAKg.png)

- `/etc` 目录是系统根路径下最重要的目录之一，它是用来存储操作系统文件的公共区域，比如 `/etc/sudoers`、`/etc/passwd` 分别记录了拥有 root 权限的用户、系统内的所有用户信息。
- `/var` 目录是系统的服务或应用频繁写入的地方，比如 `/var/log` 存储了程序的 log 文件。
- `/tmp` 里是一些程序只需要访问一两次的文件，每当系统重启时，这里的文件都会被清空。注意这个文件夹默认可被任何用户写入。
- `/home` 这个文件夹下是各个用户的家目录。
- `/mnt` 是数据卷的挂载点；通过 USB 等接口连接的外部媒体也可能出现在 `/media` 文件夹中。
- `/srv` 存放这个服务器提供的服务所需的文件（如网站资源）。

### Permissions

Linux 系统支持多用户同时登录并执行各自的任务。为了对不同用户进行管理控制，可以定义用户组（英语：group）来划分不同的访问权限。我们通过具体例子来理解。

```bash
training@SAST: /Documents/sast-training $ ls -l
total 8
drwxrwxr-x 2 training training 4096 7月  11 22:31 folder
-rw-rw-r-- 1 training training    0 7月  11 20:16 hello.txt
-rwxrwxr-x 1 training training   27 7月  11 22:33 program.sh
```

对于目录中每一项开头的 "-rw-rw-r--"（以文本 `hello.txt` 为例）字样，下图给了一些解释：

![IMG_2021-07-11T22-47-19](https://i.loli.net/2021/10/10/iJtCH3Un6uOpkKb.jpg)

+ 开头第一个字符是用于特殊权限的 `flag`，它来表明该文件的一些特殊属性如文件夹、连接等。
+ 接下来九个 flag 每三个为一组，分别代表所属用户、所属组、所有其他用户对于这个文件的 r(read)、w(write)、x(execute) 三种访问的权限。
+ 除了用 rwx 字符来表示外，还可以用二进制编码表示，其中 r 为最高位 4，w 中间位 2，x 最低位 1。例如可读写可执行的权限编码为 7，可读写不可执行为 6，可读可执行为 5 等。

对于一些重要文件，你大概不希望组内其他用户或者任意用户对它有过多的访问权限；或者对于一些刚写好的脚本文件默认为可读写不可执行，你希望能够获得执行它的权限。这时我们可以通过 `chmod` 指令来改变文件的权限设置。

```bash
chmod +x script.sh     # 给脚本添加可执行权限（默认对于任何人 (a)）
chmod 777 public.py    # 让文件可被任何人读写执行，777 等价于 a+rwx
chmod o-rw journal.txt # 禁止组外用户 (o) 的读写
```

### Shell Operations

以下是一些 Shell 中能用到的操作符。最好还是结合实际应用来掌握。

| Operator | Description                   | Example                                      |
| -------- | ----------------------------- | -------------------------------------------- |
| &        | 允许命令在后台执行            | `cp -r ./here ./there &`                     |
| &&       | 执行多条指令，逻辑和 C++ 类似 | `wget someurl/install.sh . && ./install.sh ` |
| >        | 重定向输出                    | `echo "Hello" > hello.txt`                   |
| <        | 重定向输入                    | `./main < in.txt`                              |
| >>       | 重定向输出并采取“追加”模式    | `echo cirno >> visitors.txt`                 |
| \|       | 连接前后两个程序的输出和输入  | `ls ~/Documents | grep note.txt`             |

### Read the Manual

```bash
man <command-name>
```

`man` 指令是了解各种指令具体用法的最快途径，绝大多数的指令和软件都会有对应的 Manual 提供查看，在里面你可以看到指令的名称来源、作者、描述和用法等等。

`man` 所打开的界面支持多种浏览方式，Vim 的 J/K、方向键、回车键、翻页键等都可以使用，并且支持搜索功能。

比如我们想了解 `ls` 的 `-h` 选项的作用，可以在 `man ls` 中输入 `/-h` 并回车，搜索到的 `-h` 选项就会高亮显示，继续回车找到下一个结果。`/` 是向后搜索，而 `?` 是向前搜索。

如果你想查看帮助的软件凑巧没有对应的 Manual，你总可以试试在指令名后加上 `-h / --help` 选项，通常来说命令输出错误时它就会有 Usage 的提示。

类似的软件还有 tldr(too long; didn't read)、cheat(cheatsheet)、fuck 等，它们是精简版的 man，提供了指令的常见用法。

## SSH

现在我们回到 SSH 上来。

### Executing Commands

SSH 可以直接远程执行命令而不需要连接到远程的 Shell 中，在连接命令后加上需要执行的命令：

```bash
ssh foobar@server [command]
```

### Copying Files

`scp` 指令可以从远程拷贝文件到本地/从本地拷贝文件到远程，基本格式是 `scp <from> <to>`。如果要拷贝一个文件夹，应该使用 flag `-r` 表示递归深入目录。

```bash
# 将本地当前目录的 test.zip 拷贝到远程的 ~/test.zip
scp test.zip training@153.223.56.12:~/test.zip 
# 将远程的 data.csv 拷贝到本地当前目录
scp training@152.32.34.12:~/data.csv . 
```

### Config File

每次连接服务器都要输入用户名、IP 和端口未免过于繁琐，我们可以通过编辑 SSH 配置文件来简化命令。这个配置文件位于家路径的 `.ssh` 文件夹下，名字为 `config`（如果不存在需自己创建），即 `~/.ssh/config`。对于 Windows 系统而言，这个 `.ssh` 文件夹位于用户目录下 `C:\Users\<username>\.ssh\config`。配置文件写法如下：

```
HOST sast
    HostName 152.136.177.53
    Port 8000     (默认22，此时不需要指定)
    User root
```

这样每次我们想连接到 `root@152.136.177.53 -p 8000` 时只需要输入 `ssh sast` 即可。

这个配置文件同样适用于 `scp` 命令：

```
# 前一小节例子的简略版
scp test.zip sast:~/test.zip 
scp sast:~/data.csv . 
```

### Key Authentication

SSH 配置文件并不能指定登录时的密码（显然这样会很不安全），所以我们登录时还是要输入密码。能不能连密码都不用输入？

利用密码学中的公钥-私钥非对称加密机制，我们只需要在服务器中预存好自己的公钥，就可以在登录时自动化验证流程而免去密码。这个过程的关键是向服务器 Alice 证明自己是持有私钥的那个客户端 Bob：Alice 用 Bob 预存的公钥加密一个用于验证的串发给 Bob，Bob 用私钥解密得到这个串后发给 Alice，就能证明自己确实持有与公钥对应的私钥。

![Screenshot from 2021-07-15 10-59-22](https://i.loli.net/2021/10/10/nhiC3kRQ9ZVgoBD.png)

**密钥生成**

```bash
ssh-keygen
```

按照命令行提示流程，默认在 `~/.ssh` 下生成公钥 `id_rsa.pub` 和私钥 `id_rsa`，后者需要妥善保存。通过 `ssh-keygen -y -f ~/.ssh/id_rsa` 检验密钥的存在。

**在服务器中存放公钥**

在接受 SSH 连接请求时，服务器的 SSH 会在目标用户的 `~/.ssh/authorized_keys` 中查找公钥。因此我们需要将自己的 `id_rsa.pub` 中的内容追加到服务器的 `authorized_keys` 中。

```bash
cat ~/.ssh/id_rsa.pub | ssh training 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys' 
# Windows 下路径为 ~/.ssh/id_rsa.pub
```

上面的命令完成了将本地公钥保存到远程服务器的过程，经过之前的学习，你能理解这个命令是如何执行的吗？

`ssh-copy-id` 提供了更简便的解决方案：`ssh-copy-id -i ~/.ssh/id_rsa.pub training`。

## Basic Tools

### Package Manager

**软件包管理系统**是在电脑中自动安装、配置、卸载和升级软件包的工作组合。使用软件包管理系统可以大大简化在 Linux 发行版中安装软件的流程。常见的软件包有两种类型：deb 软件包（由 dpkg 和它的前端 apt 管理，使用于 Debian、Ubuntu）和 RPM 软件包（由 dnf、yum、ZYpp 等前端管理）。

绝大多数的 Linux 发行版或类 Unix 系统都有它的包管理器，Ubuntu 有 `apt`，Arch Linux 有 `pacman`，macOS 有 `Homebrew`。

包管理器的指令一般都很符合直观，如果失败了可以通过 `man` 或者 `help` 查看用法。

系统中包管理器所使用的仓库往往默认位于国外，这使得每次从仓库拉取东西时耗费很长时间或者甚至连不上。好在我们国内有许多的镜像源，比如清华的 TUNA，访问 https://mirrors.tuna.tsinghua.edu.cn/help 可以找到 Ubuntu 在内的各种仓库镜像源配置方法。

Homebrew 的安装方法：

```bash
/bin/bash -c \
"$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Vim

`vimtutor` explains it all. `vimtutor -g zh` for Chinese learner.

![hottest_editors](https://i.loli.net/2021/10/10/DFpv8LzXEIRiCPr.png)

### tmux

我们每次打开终端或是 SSH 登录远程服务器时，都是创建了一个会话（英文：session）来进行临时的交互，此时窗口（或连接）是和它开启的进程（比如 Shell）绑定在一起。当我们关闭窗口或者断开连接时，对应的进程也随之终止了。

为了在会话结束后其开启的进程还能继续进行，我们需要先进行"解绑"，在后续需要时再重新进行绑定。tmux 完成的就是这样一个任务。

```bash
tmux            # 创建新的 tmux 会话
tmux detach     # 分离当前 tmux 会话，快捷键 Ctrl+B D
tmux ls         # tmux list-session
tmux attach -t <session-name> # Sessions are labeled by numbers by default
tmux kill-session -t <session-name>
```

除了能够让任务持续进行，tmux 还有一个很重要的功能是分窗口和分屏。

```bash
Ctrl+B %      # Split horizontally
Ctrl+B "      # Split vertically
Ctrl+B 方向键  # Switch between panes
# ...
```

这一系列操作的快捷键基本上都是 Ctrl+B xxx，请自行学习掌握。

附：https://tmuxcheatsheet.com/

## 后续拓展

想要学习更多 Linux 内容？

+ TryHackMe "Linux Fundamentals" Series https://www.tryhackme.com
+ Missing Semester https://missing.csail.mit.edu/

## 参考资料

+ 2021 暑培讲义 by JuneTheRiver
+ 菜鸟教程 https://www.runoob.com/linux/linux-tutorial.html
