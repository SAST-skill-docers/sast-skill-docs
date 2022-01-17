## Python 包管理

### 前置知识

+ 你所使用的操作系统的常用命令行操作
+ 知道 Python 是什么，以及 Python 如何启动
+ 知道 Python 可以安装不同的第三方库

### 安装包管理工具

**pip**

pip 是用于管理 Python 库的工具，但是安装 Python 时一般并不会自动安装 pip，因此你看需要手动安装。

你可以使用包管理器安装 `python3-pip` 来安装 pip，也可以使用也可以使用官方脚本：

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py # 注意这个地方使用什么版本的 Python 就会安装什么版本的 pip
```

**Conda**

尽管在前文中我们已经讲过如何安装 Python 解释器，但是在需要复杂的环境管理（或者想要更简单地安装）时，我们常常会用到 Anaconda 或精简版 Miniconda。Anaconda 本⾝⾃带了 UI 以及⼀堆包，体积庞⼤，如果你有兴趣可以选择安装它，此处我要讲的是它的精简版 Miniconda，删掉了⼤量预装包，留下了少数包与管理部分。下⾯的教程针对 Miniconda 而⾔，如果你选择安装 Anaconda 同样可以参考下⾯的教程，因为 Miniconda 的功能是它的⼦集，少了图形界⾯与⼀些预装包。

下载我推荐⽤ TUNA 源，⾼效便利：

- [Miniconda3-py39_4.9.2-Windows-x86_64.exe](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.9.2-Windows-x86_64.exe)：Miniconda for Windows
- [Miniconda3-py39_4.9.2-MacOSX-x86_64.sh](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.9.2-MacOSX-x86_64.sh)：Miniconda for Mac
- [Miniconda3-py39_4.9.2-Linux-x86_64.sh](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh)：Miniconda for Linux
- [Anaconda3-2021.05-Windows-x86_64.exe](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.05-Windows-x86_64.exe)：Anaconda for Windows
- [Anaconda3-2021.05-MacOSX-x86_64.sh](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.05-MacOSX-x86_64.sh)：Anaconda for Mac
- [Anaconda3-2021.05-Linux-x86_64.sh](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.05-Linux-x86_64.sh)：Anaconda for Linux

注意这里的 Mac 只有 x86_64 版本，使用 M1 Mac 的同学暂时无法安装 Conda。

### 换源

如果用过其他任何一种包管理器的话，想必已经熟悉了换源这个话题，即使没有受到 ~~众所周知~~ 神秘原因影响的源服务器，仍然受到距离原因的影响，效果没有那么理想。这里建议使用由 TUNA 协会维护的 TUNA 源，可以使用一键配置脚本 oh-my-tuna ：

```bash
wget https://tuna.moe/oh-my-tuna/oh-my-tuna.py
python3 oh-my-tuna.py
```

顺利的话，你将看到类似如下的输出：

```bash
[pypi]: Activating...
[pypi]: Mirror has been activated
[Anaconda]: Activating...
[Anaconda]: Mirror has been activated
```

再执⾏（如果使用 pip）：

```bash
pip config list
```

这将得到（或包含）如下输出：

```bash
global.index-url='https://pypi.tuna.tsinghua.edu.cn/simple'
```

或者再执⾏（如果使用 Conda）：

```bash
conda config --show-sources
```

这将得到（或包含）如下输出：

```bash
channels:
- https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
- https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
- defaults
```

这分别表明你的 pip 和 Conda 已经成功设置成了 TUNA 源。

!!! caution "如果在 Windows 上运行遇到问题"

    截止写这句话的时候 oh-my-tuna 还有个小问题没有修，在 Windows 上尝试判断系统类型的时候可能引发一个未被处理的 exception，从而导致脚本退出，作为一种临时的修补方案，你可以将 622 行改为 `if uname is None or any(map(lambda x: x in uname, no_suffix_list)):` 后重新运行该脚本。

### 安装和卸载第三方库

使用 pip 或 Conda 可以轻松地管理本地的 Python 包，例如安装爬虫常用的 BeautifulSoup4 库如下：

```bash
pip install BeautifulSoup4
conda install BeautifulSoup4
```

而卸载只需：

```bash
pip uninstall BeautifulSoup4
conda remove BeautifulSoup4
```

**使用 IPython**

作为一个无关紧要但是会让你很爽的建议，在使用交互式窗口时可以选择使用 IPython 代替原生的 Python 解释器。相较于本地的 Python Shell，IPython 提供了更为强大的编辑和交互功能（如果你用 Pycharm 的话会得到更好的体验），我们使用 pip 来安装 IPython：

```bash
pip install ipython
conda install ipython
```

然后执行：

```bash
ipython
```

就可以得到一个更友好的 Python 交互式窗口，同时 IPython 支持一些 Shell 命令，如 `cd`、`ls` 等（非内置的 Shell 命令可以通过前面加 `!` 转义， IPython 将会使用原生的 Shell 执行这一行命令），这相比于通常情况下使用 `os.listdir()` 显然会方便一些，而退出 IPython 也只需要 `exit` （而不是 `exit()` ）。

### 虚拟环境

有些时候我们可能在不同的场合需要不同的一些库，而这些库之间并没有很好的兼容性，或者有些时候我们会 ~~需要使用旧版的特性~~ 没有适配新版本的 Python，种种原因可能导致需要同时安装多个不同版本的 Python 并使用好几套互斥的库，这个时候就需要 ~~反复安装卸载~~ 对这些环境做出隔离，可以通过 Conda 来管理不同的虚拟环境。安装好 Conda 后可以看到命令行中多了一个 `(base)`，这表明目前 Conda 启用的是默认的环境。

### 资源链接

+ 2020 / 2021 暑培 Python 环境配置文档
+ Pip Docs: <http://pip.pypa.io/>
+ Conda Docs: <https://docs.conda.io>