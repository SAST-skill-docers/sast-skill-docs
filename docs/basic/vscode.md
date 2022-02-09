# VSCode 配置

VSCode 是一款**轻量级**的**代码编辑器**，可以通过安装各种各样不同**扩展**的方式来实现开发者所需要的功能。

!!! note "区分：代码编辑器（Editor）与集成开发环境（IDE, Integrated Development Environment）"

    代码编辑器事实上我们可以看成是一个记事本（没错，如果是 Windows 用户，就是你按下 Win+R 输入 notepad 回车之后的那个记事本），其最基本的功能是文档编辑。不过之所以将其称为是**代码编辑器**，是因为它虽然继承自一般的文档编辑器，又具备了一些一般的文档编辑器所不具备的功能。具体来说，例如自动语法高亮，自动补全，甚至是自动代码重构等等。
    
    集成开发环境（IDE）是一种用于构建应用程序的软件，**可将常用的开发人员工具合并到单个图形用户界面**（GUI）中。具体来说，我们只需要简单的点击按钮，可能就可以完成程序的编译、链接、运行、调试等等工作。而这些工作在最初都是需要人手工在命令行中完成的。我们在《程序设计基础》课程中最初使用的 Dev-C++ 便是一个集成开发环境。
    
    我们今天要介绍的 VSCode 是一款轻量级的**代码编辑器**。如果没有各种扩展插件的支持的话，可能我们只能把它称作是大号的 Notepad++，而正是因为社区中各种各样的扩展，VSCode 才得以展现其强大。

![image-20220209114623794](https://s2.loli.net/2022/02/09/psKk8yJ2CxMic1O.png)

本文我们介绍 VSCode 的基本配置，重点在于介绍一些未来可能会用到的扩展插件。按照本文的流程完成后，你应该等效地完成了足以应对《面向对象程序设计基础》课程中的代码的集成开发环境的配置。此外，我们还会介绍一些基本的调试方法，而这恰恰是 VSCode 比 Dev-C++ 在现阶段不知道高到哪里去的地方。

事实上，在未来，我们可以用 VSCode 配置 Python 开发环境，配置软件工程课程中所需要的框架的开发环境，而这些都是通过“扩展”（Extension）来实现的。

<!--more-->

## 前置知识

+ 了解一个 C++ 程序从源代码到可执行程序中发生了编译、链接这两步操作（《程序设计基础》大作业中会有此部分的讲解）
+ 动手能力

## 下载与运行

+ 打开 VSCode 官网：https://code.visualstudio.com/
+ 点击大大的 Download 按钮（如果是 Windows 64 位用户可以点击下拉框选择 x64 安装包版本）
+ 进行安装或解压缩（注意路径中不能存在任何中文字符，推荐仅用字母和数字）
+ 到你安装 VSCode 的目录下，新建 `data` 文件夹

> 这里我们新建 `data` 文件夹后，之后 VSCode 运行时的扩展插件和用户数据便都会存放在 `data` 文件夹下，这样可以在一定程度上避免系统盘容量占用的问题。如果不新建 `data` 文件夹，那么 VSCode 会将上述插件和用户信息存放在系统盘的用户目录下。
>
> ![image-20220209213021194](https://s2.loli.net/2022/02/09/IsPShQ2nLyqmwH8.png)

## C++ 相关开发环境配置

### 环境变量与 Mingw64

我们可以先打开终端（Windows 用户请使用 Win+R，输入 `cmd`），尝试输入：

```bash
g++ --version
```

如果你的终端返回了 `g++` 的版本信息，恭喜，您 C++ 编译器的环境变量配置正确，可以跳过这一小节。当然，如果想更加详细地了解环境变量是什么，可以继续阅读这一小节。

而如果返回：

```bash
'g++' 不是内部或外部命令，也不是可运行的程序
或批处理文件。
```

那么则说明你的终端不知道 `g++` 是什么命令。我们接下来将首先讲解环境变量是什么，然后再为大家讲述该怎么配置环境变量。

!!! note "什么是环境变量？"

    当你的 Shell 在执行命令时，会尝试在**一系列路径**下搜索同名的可执行文件。这一系列路径我们就称作是环境变量。
    
    Windows 用户可以在终端中输入 path 来查看当前环境变量（由于我进行过一系列配置，所以其输出结果可能与你的不同）：
    
    ```bash
    D:\Coding>path
    PATH=D:\Anaconda;D:\Anaconda\Library\mingw-w64\bin;D:\Anaconda\Library\usr\bin;......
    ```
    
    Linux 用户可以通过使用 `echo $PATH` 来查看自己的环境变量：
    
    ```bash
    c7w@cc7w > /mnt/d/Coding > echo $PATH
    /home/c7w/.local/lib/python3.8/site-packages:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/mnt/d/Anaconda:/mnt/d/Anaconda/Library/mingw-w64/bin:/mnt/d/Anaconda/Library/usr/bin:/mnt/d/Anaconda/Library/bin:/mnt/d/Anaconda/Scripts:/mnt/f/VM/bin/:/mnt/c/Program Files (x86)/Common Files/Intel/Shared Libraries/redist/intel64/compiler:/mnt/c/Program Files/Common Files/Oracle/Java/javapath:......
    ```
    
    没错，就是这一系列文件夹。回忆我们刚才执行 `g++ --version` 命令的时候，我们的终端会在这一系列文件夹下为我们寻找叫做 `g++` 的可执行文件。如果找到了名为 `g++` 的可执行文件，我们的终端就会将参数传入，将其执行；而如果我们的终端没有找到，那么就会向我们报告“未知命令”。
    
    我们刚刚已经理解了“环境变量（Path）”的运行逻辑，接下来我们讲解如何进行环境变量的配置。`
    
    对于 Windows 用户，请按下 Windows + S 打开搜索框，在其中输入 `path`，然后选择"编辑系统环境变量"，进而选择“环境变量”，然后选择“系统变量”中的 Path 字段，双击打开后即可配置。
    
    ![image-20220209214212982](https://s2.loli.net/2022/02/09/L9DhPnOjNZ58gCV.png)
    
    对于 Linux 用户，环境变量是绑定在你的 Shell 上的，不同的 Shell 有不同的配置方式。这里我们提供一个[链接](https://www.cnblogs.com/youyoui/p/10680329.html)帮助您了解环境变量的相关配置。

!!! note "什么是 Mingw64？"

    MinGW 的全称是：Minimalist GNU on Windows。
    
    它实际上是将经典的开源 C 语言编译器 GCC 移植到了 Windows 平台下，并且包含了 Win32API，因此可以将源代码编译为可在 Windows 中运行的可执行程序。而且还可以使用一些 Windows 不具备的，Linux平台下的开发工具。
    
    一句话来概括：MinGW 就是 GCC 的 Windows 版本。这是将你写的 C/C++ 语言的源代码编译成汇编代码，进而链接成可执行文件的工具。之前我们的 Dev-C++ 事实上也集成了这个工具。

我们下载官方的安装工具（[链接](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/installer/mingw-w64-install.exe)），下载完成后右键以管理员身份运行，`Architecture` 中 `i686` 是针对于 32 位系统，`x86_64` 针对于 64 位系统，其他不用修改，进入下一步选择安装目录即可。然后即可等待安装程序下载并进行安装。

然后，我们需要进行环境变量配置。进入 `Mingw64 的安装路径/mingw64/bin/`，我们可以看到 `gcc`，`g++` 这些我们熟悉的可执行文件。**然后我们需要做的就是将这个文件夹添加至环境变量中。**

然后我们就可以打开终端，输入 `g++` 验证环境变量是否配置成功。（请注意，在环境变量配置后需要重启终端才能生效）

### Make

同时，针对于我们之后课上所需要用到的 `make` 命令，我们可以将同目录下的 `mingw32-make.exe` 复制一份，改名为 `make.exe`，这样我们也能正常使用 `make` 命令了。

由于 Linux 平台自带了 `gcc` 和 `make` 等等编译工具，所以这里不需要进行额外的配置。

## 扩展插件的安装

我们打开 VSCode，先简单介绍下界面及其功能：

![image-20220209220846472](https://s2.loli.net/2022/02/09/pM6kzGH4xbIRW5K.png)

红色框是我们当前项目（即文件夹）下的所有文件清单，蓝色框是我们编写代码的地方，绿色框是我们的应用商店。

这里我们推荐几个扩展，在应用商店中搜索即可下载：

+ Chinese (Simplified) Language Pack for Visual Studio Code：语言
+ Code Runner：调试用
+ C/C++：支持在 Code 中调试 C/C++ 程序
+ Remote - SSH：之后进行远程开发会用到，这里不多做介绍

在安装了简体中文插件后，我们可以按 `Ctrl + Shift + P`，打开输入框，输入 `Configurate display language`，选择中文后重新启动即可。

## 代码调试

如何运行一段代码呢？很简单，我们只需要在**对应的代码界面**按下 `F5` 或是在菜单栏找到“运行 > 启动调试”，便可以启动调试模式：

![image-20220209221547145](https://s2.loli.net/2022/02/09/x6yBiPzfVAQsErZ.png)

在配置中选择 `C++ (GDB)`，进而选择 `g++.exe`。

然后我们会发现项目路径下生成了一个 `.vscode` 文件夹，内含 `tasks.json` 和 `launch.json`，这两个文件分别有什么作用我们即将就会进行介绍。

![image-20220209221657511](https://s2.loli.net/2022/02/09/pwDenGirIJZdjsv.png)

（你的界面排版可能和我有所不同，不过主要功能是大同小异的，当你熟练运用了 Code 之后你可以自行将这几个框框拖来拖去摆到你觉得舒适的位置）

我们接下来将分单文件项目和多文件项目进行讨论，说明一些在 Code 中调试 C++ 代码的技巧。

### 单文件项目

单文件项目指只有一个 cpp 文件的项目，`main()` 函数的定义就在其中，我们在《程序设计基础》课程的大部分平时作业都是这种项目。

这种项目我们直接按 `F5` 便可进行运行，其输出结果会在“终端”选项卡中出现。

![image-20220209222124275](https://s2.loli.net/2022/02/09/UO5mv1K3VBcN2lL.png)

这是一段演示单向平方和双向平方探测在哈希表中可以占用的位置的示例程序，在这里仅做说明使用，大家不必理解其背后的原理。（事实上你给别人调代码的时候不都是这样嘛，对着看不懂的逻辑满脸黑线.jpeg）

想要在程序运行过程中设置断点，我们只需点击对应的行号：

![image-20220209222401148](https://s2.loli.net/2022/02/09/ZVFLrcwSbaOdxTm.png)

在这里设置断点，程序将会在第 30 行执行完成，第 31 行将要执行的时候触发断点，让我们看一看：

![image-20220209222500324](https://s2.loli.net/2022/02/09/5tHAOwQBdh1Sqcs.png)

红框，也就是“终端框中，我们的程序输出了前半段运行时产生的 `cout` 信息。接下来我们将说明该如何查看中间变量：

![image-20220209222635735](https://s2.loli.net/2022/02/09/UEioypzr9fa57GK.png)

比如程序在命中这个断点时，我们有以下途径获取中间变量的值：

在黄色框“变量”中，我们可以看到程序自动追溯的局部变量和寄存器值。在蓝色框“监视”中，我们可以自行定义一些需要追溯的变量。在红色框中，我们可以直接输入变量名来查看其对应的值。

![image-20220209222812673](https://s2.loli.net/2022/02/09/jAlCZgHIYphwoDv.png)

然后，我们来将目光放在上述这几个按钮身上。

+ 继续（F5）按钮将会使程序继续执行，直到命中下一个断点或是到程序结尾。
+ 单步跳过（F10）按钮对于程序来说，如果将要执行的行调用了某些函数，那么将直接将本行执行完毕，进入下一行。
+ 单步调试（F11）按钮对于程序来说，如果将要执行的行调用了某些函数，那么将进入将要执行的函数内部。
+ 停止（Shift+F5）按钮终止当前调试工作。

### 多文件项目

对于多文件项目的调试来说，我们重点关注点在于 `.vscode` 下两个文件的配置。

首先我们将我们的示例程序修改为多文件项目：

![image-20220209223422595](https://s2.loli.net/2022/02/09/IBLJmUG7NCy1ikv.png)

我们采用最简单的方式，将我们的单文件项目魔改成多文件。

我们回忆如果使用命令行，该如何将我们的程序编译：

```bash
g++ hash.cpp another.cpp -o main
```

没错，我们现在配置 Code 使得其在“生成目标文件”任务中执行上述命令。

`tasks.json` 负责可执行文件的生成，我们主要进行如下修改：

```json
{
    "tasks": [
        {
            "type": "shell", // cppbuild -> shell
            "label": "C/C++: g++.exe 生成活动文件",
            "command": "D:\\Mingw\\mingw64\\bin\\g++.exe",
            "args": [
                "-g",
                "hash.cpp", // ${file} -> your source code list
                "another.cpp",
                "-o",
                "${fileDirname}\\main.exe" // main.exe
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

`launch.json` 主要负责调试目标程序，我们做以下修改：

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++.exe - 生成和调试活动文件",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\main.exe", // main.exe
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "D:\\Mingw\\mingw64\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: g++.exe 生成活动文件"
        }
    ]
}
```

然后我们就可以愉快地调试多文件项目了。

## 后续学习

+ 自行研究 Remote-SSH 的使用方法，如何连接到外部服务器进行开发
+ 自行研究 Python 配置调试环境的方法
+ 在《软件工程》课程中，使用 Code Prettier 插件 + ESLint 规范项目

## 资源链接

+ https://code.visualstudio.com/
+ https://zhuanlan.zhihu.com/p/76613134
+ https://blog.csdn.net/linjf520/article/details/108559210

!!! note "Tips"
    配置开发环境往往是在学习的过程中最恼人的一件事，但是不用心急，常言道“良好的开端是成功的一半”。如果实在遇到配置问题，在进行搜索无法解决后，建议向同年级/学长/答疑坊进行求助。