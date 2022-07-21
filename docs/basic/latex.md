# LaTeX

虽然 Markdown 很好用，但是生成的 pdf 文档看起来就是<s>没有范</s>不够正式。此外，使用 LaTeX 也是我们之后写论文的必备技能。

本教程主要涉及已对 Markdown 较为熟识之后的迁移学习。

!!! note
    实际上，你可以在 Markdown 里使用 LaTeX 的子集 [KaTeX](https://katex.org/)

## 前置知识

+ Markdown 入门
+ Markdown 编写数学公式的方法

## LaTeX 简介

我们首先解决以下几个问题：

+ LaTeX 是什么？
+ LaTeX 怎样运作？
+ 我该在哪里写 LaTex？

在解决上述几个问题之后，我们再详细介绍该怎么写 LaTeX 的问题。

### LaTeX 是什么？

相信你读到这里已经做好觉悟要被灌输一系列关键词和其对应概念了。让我们开始：

> 历史回溯到 Knuth 教授的巨著 The Art of Computer Programming 将要出版之际，当出版商将他们排版的书稿草样交给 Knuth 教授的时候，他对于其中复杂数学公式的排版处理十分不满。其排版之粗糙，已达到了会影响人们理解原书内容的程度。因此，对于复杂的数学和物理公式，我们急需一种能够将其在互联网上传输的编码格式，使得人能阅读的公式和机器能存储的公式之间达到一种互相转化。

+ **TeX**：一种排版引擎，也是该引擎使用的标记语言的名称。引擎是指能够断行、分页的程序，标记语言是控制命令和文本结合的格式。可以类比理解成你写的 C++ 源代码或者更底层的机器指令码，如输入 `$2^6$`。
+ **LaTeX**：是一个基于 TeX 的排版系统，将用户按照它的格式编写的文档解释成 TeX 引擎能理解的形式并交付给 TeX 引擎处理，再将最终结果返回给用户。可以类比理解成 g++ 编译器，将上述代码渲染为 $2^6$。
+ **pdfTeX** 与 **pdfLaTeX**：原版 TeX 系统生成的文件是 `dvi` 格式，而 pdfTeX 系统下生成的文件是 `pdf` 格式。
+ **XeTeX** 与 **XeLaTeX**：上述 TeX 系统生成的字符集只支持 ASCII 字符。在 XeTeX 出现之前，我们曾使用过引用引入 CJK 宏库（解决不支持中日韩字符问题的一个库）手段来处理中文字符的问题。但是这个排版系统对所有 Unicode 字符都实现了支持。
+ **LuaTeX** 与 **LuaLaTeX**：`pdfTeX` 系统的继承者，支持使用一些用户自定义脚本来实现之前需要写成 TeX 的功能。支持 Unicode，内联 lua，支持 OpenType。

这里我们推荐使用 `XeTeX` 系统来进行我们日常的工作，我们后续的教程也围绕这个排版系统展开。为了简洁起见，我们后续不再区分上述概念，统一使用 `TeX`，`LaTeX` 来表述我们在说的这个话题。

### LaTeX 怎样运作？

LaTeX 排版系统的输入是含有我们敲的文本和控制命令的 `tex` 文件，输出是一份 `pdf` 文件。我们只需要负责在 `tex` 文件中写下源码，然后剩下的编译和生成工作全部交给 LaTeX 即可。

有时我们还可以把一个 LaTeX 项目组织成一个文件夹，此时还是一份 `tex` 文件决定一个 `pdf` 的生成，但是我们还可以在这个文件夹中引入其他一些文件，如字体文件，图片文件，`.cls` 文件（文档模板类文件）等等。此外，我们还可以在这个文件夹中编写多个 `tex` 文件，以共享文件夹中的其他资源。此时不同的 `tex` 文件之间甚至可以项目包含（类比于 C++ 的 `#include` 包含）。

### 在哪里编写 LaTeX？

我们有离线和在线两种模式来撰写 LaTeX。

离线模式就是安装一个 LaTeX 排版系统，类比我们想写 Markdown 的时候装了一个 Typora 软件一样，我们可以安装相应的软件来辅助我们工作，如：

+ TeXworks
+ TeXstudio

而使用这种方式安装带来的问题是可能安装包过于臃肿，优点是不用受到网络环境等等因素的干扰，也不用受到网络环境存储容量或运行时环境的限制。其安装方式在网络上搜索“LaTeX 入门”便可找到堆积如山的[教程](https://www.zhihu.com/question/62943097)。

而我们这里推崇的方式就是使用在线方式来编写。如 `Overleaf` 在内的托管网站会将你的每个 TeX 项目组织成一个仓库的形式，并允许你在其中进行在线编辑：

![image-20220213203750216](https://s2.loli.net/2022/02/14/JcqCKNfs31vxm2u.png)

![image-20220213204115880](https://s2.loli.net/2022/02/13/IKAPDlYTmFEu5S1.png)

左上角是我们当前仓库的文件清单，较左侧窗口是编辑器，右侧窗口是即时预览窗口。类似于 `Overleaf` 的网站甚至还提供了仓库权限管理系统，你可以邀请其他人一起编辑，或是导入别人编辑好的模板继续你的编辑等等。值得一提的是许多学术会议都会给出他们接受的论文的模板。

鉴于 `Overleaf` 需要科学上网才能访问：

+ 贵校 TUNA 协会维护了一份 [Tsinghua Overleaf](https://overleaf.tsinghua.edu.cn/login)，需要使用清华统一认证登录; 
+ 贵校贵系贵协网络部维护了一份自己的基于 `Overleaf`  的 LaTeX 在线编辑网站 [TeX9](https://stu.cs.tsinghua.edu.cn/tex9/)，<u>需要使用酒井 ID 才能进行登录</u>。

我们接下来的演示便是基于 TeX9。

## LaTeX 编写基础

说是编写基础，接下来我们就要像介绍 Markdown 一样，先简单罗列一些简单的文档控制命令。在基础篇中我们先仅仅介绍怎样实现从 Markdown 到 LaTeX 的迁移。对于其中一些文档控制命令，我们将会在后续教程详细说明。

这里提供 CheatSheet 供查阅：

![image-20220214004156979](https://s2.loli.net/2022/02/14/KP5yeuotcJhTlVO.png)

![image-20220214004207594](https://s2.loli.net/2022/02/14/j3EHglvqhoetRi4.png)

### Hello, World!

![image-20220213205714169](https://s2.loli.net/2022/02/13/HPCaZJUtNOj74uF.png)

上述便是一份 `tex` 文件的示例，我们推荐你新建一个项目，然后将下面我们要介绍的内容一一尝试。

### 支持中文字符

首先，我们上述已经介绍过，支持中文字符的方式有二，一种是引入 `CJK` 宏包，另一种是使用 XeLaTeX 编译器并对源码做适当修改。这里我们采用第二种方式。

首先，按下你项目左上角的 Menu 按钮，然后在 Compiler 选项中选择 XeLaTeX 选项。

然后，输入以下内容：

```latex
\documentclass[UTF8]{ctexart}
\begin{document}
你好，world!
\end{document}
```

这样我们就完成了中文字符的引入。至于 `documentclass` 是什么，我们将在后续介绍。

### 导言与文档信息

```latex
\documentclass[UTF8]{ctexart}
\title{Sample Document}
\author{c7w}
\date{\today}

\begin{document}

% 这条控制命令会读取导言部分的文档相关信息
% 并将其渲染到文档中
% 事实上可以参考相关宏包的 Doc：
% http://texdoc.net/texmf-dist/doc/latex/titling/titling.pdf
\maketitle 

你好，world!

[在这里你就开始写你的作业第一题了]

\end{document}
```

![image-20220213210910831](https://s2.loli.net/2022/02/13/IbNdaCD2wFXVlAq.png)

### 章节与段落

```latex
\documentclass[UTF8]{ctexart}
\title{Sample Document}
\author{c7w}
\date{\today}

\begin{document}

\maketitle

\section{我是 Section 标题}

我是 Section 介绍。

\subsection{我是 Subsection 标题}
我是 Subsection 介绍。

\subsubsection{我是 Subsubsection 标题}
我是 Subsubsection 介绍。

% \subsubsubsection{不能继续套 sub 了，到底了}

\paragraph{我是 Paragraph 标题}
我是 Paragraph 后面跟着写的东西。
本人也是经过了深思熟虑，在每个日日夜夜思考这个问题。
我们都知道，只要有意义，那么就必须慎重考虑。
这种事实对本人来说意义重大，相信对这个世界也是有一定意义的。
要想清楚，一天掉多少根头发，到底是一种怎么样的存在。
贝多芬曾经说过，卓越的人一大优点是：在不利与艰难的遭遇里百折不饶。


\subparagraph{我是 Subparagraph 标题}
我是 Subparagraph 后面跟着写的东西。
这不禁令我深思既然如何，一天掉多少根头发的发生，到底需要如何做到，不一天掉多少根头发的发生，又会如何产生。 
总结的来说， 所谓一天掉多少根头发，关键是一天掉多少根头发需要如何写。 生活中，若一天掉多少根头发出现了，我们就不得不考虑它出现了的事实。 郭沫若曾经说过，形成天才的决定因素应该是勤奋。这不禁令我深思这种事实对本人来说意义重大，相信对这个世界也是有一定意义的。 我们一般认为，抓住了问题的关键，其他一切则会迎刃而解。

\subsection{这是第二节}
\paragraph{第二节} 的首段。
\subparagraph{第二节}的第二段。

\end{document}
```



![image-20220213211528785](https://s2.loli.net/2022/02/13/kIeOlBjF7uzJ5ZH.png)

在文档类 `article`/`ctexart` 中（文档类的概念我们会在进阶篇中提供指导），我们使用这些控制序列来调整行文组织结构。他们分别是：

- `\section{·}`
- `\subsection{·}`
- `\subsubsection{·}`
- `\paragraph{·}`
- `\subparagraph{·}`

### 文档目录

我们尝试在渲染区 `\maketitle` 后加入如下控制命令：

```latex
\tableofcontents
```

没错，正如你所想的，这就会生成文档的 TOC：

![image-20220213211830033](https://s2.loli.net/2022/02/13/sSI8byUWJjYLkPw.png)

### 数学公式

#### 行内公式与行间公式

首先引入相应包 **amsmath**，然后我们简单介绍公式的引入：

```latex
\documentclass[UTF8]{ctexart}

\usepackage{amsmath} % 注意这里引入相应包

\title{Sample Document}
\author{c7w}
\date{\today}

\begin{document}

\maketitle

\tableofcontents

\section{我是 Section 标题}

我是 Section 介绍。

\subsection{这个 Section 我们介绍数学公式的写法}

\subsubsection{行内公式}

% 行内公式基本可以照搬 Markdown 的模式。

初始处理 1 - 5 位的初始字符串集合需要处理 $18 + 18^2 + 18^3 + 18^4 + 18^5 = 2*10^6$ 的数据，因此需要 $O(T)$ 的时间，这里 $T=2*10^6$。

\subsubsection{行间公式}

% 行间公式用 $$ $$ 或者 \[ \] 来框住都可以，但在 LaTeX 中前者会改变行文的默认行间距，因此不推荐采用。

\[
\text{dp}[i] = \text{dp}[next[i]]+1, next[i] > 0.
\]


\end{document}
```

![image-20220213213336585](https://s2.loli.net/2022/02/13/CI2hzmUEwluDp6s.png)

#### 上下标、根式与分式

+ 上下标请使用 `^` 与 `_`
+ 根式与分式请使用 `\sqrt{·}` 与 `\frac{·}{·}`
  + 在行间公式和行内公式中，分式的输出效果是有差异的。如果要强制行内模式的分式显示为行间模式的大小，可以使用 `\dfrac`, 反之可以使用 `\tfrac`

#### 运算符

一些小的运算符，可以在数学模式下直接输入；另一些需要用控制序列生成，如

```
\[ \pm\; \times \; \div\; \cdot\; \cap\; \cup\;
\geq\; \leq\; \neq\; \approx \; \equiv \]
```

连加、连乘、极限、积分等大型运算符分别用 `\sum`, `\prod`, `\lim`, `\int` 生成。他们的上下标在行内公式中被压缩，以适应行高。我们可以用 `\limits` 和 `\nolimits` 来强制显式地指定是否压缩这些上下标。例如：

```
$ \sum_{i=1}^n i\quad \prod_{i=1}^n $
$ \sum\limits _{i=1}^n i\quad \prod\limits _{i=1}^n $
\[ \lim_{x\to0}x^2 \quad \int_a^b x^2 dx \]
\[ \lim\nolimits _{x\to0}x^2\quad \int\nolimits_a^b x^2 dx \]
```

多重积分可以使用 `\iint`, `\iiint`, `\iiiint`, `\idotsint` 等命令输入。

```
\[ \iint\quad \iiint\quad \iiiint\quad \idotsint \]
```

![image-20220213214301901](https://s2.loli.net/2022/02/13/VeI1zToC3JfAyK9.png)

#### 定界符

各种括号用 `()`, `[]`, `\{\}`, `\langle\rangle` 等命令表示；注意花括号通常用来输入命令和环境的参数，所以在数学公式中它们前面要加 `\`。

因为 LaTeX 中 `|` 和 `\|` 的应用过于随意，amsmath 宏包推荐用 `\lvert\rvert` 和 `\lVert\rVert` 取而代之。

为了调整这些定界符的大小，amsmath 宏包推荐使用 `\big`, `\Big`, `\bigg`, `\Bigg` 等一系列命令放在上述括号前面调整大小。

#### 省略号

省略号用 `\dots`, `\cdots`, `\vdots`, `\ddots` 等命令表示。`\dots` 和 `\cdots` 的纵向位置不同，前者一般用于有下标的序列。

#### 矩阵

`amsmath` 的 `pmatrix`, `bmatrix`, `Bmatrix`, `vmatrix`, `Vmatrix` 等环境可以在矩阵两边加上各种分隔符。

```
\[ \begin{pmatrix} a&b\\c&d \end{pmatrix} \quad
\begin{bmatrix} a&b\\c&d \end{bmatrix} \quad
\begin{Bmatrix} a&b\\c&d \end{Bmatrix} \quad
\begin{vmatrix} a&b\\c&d \end{vmatrix} \quad
\begin{Vmatrix} a&b\\c&d \end{Vmatrix} \]
```

![img](https://s2.loli.net/2022/02/14/uix67cYI4UaXvK9.jpg)

#### 多行公式

可以用 `aligned` 环境来实现，用 `&` 实现位置对齐。

```latex
\[
\begin{aligned}
x = a+b+c+ \\
+d+e+f+g+d+d+d+d+d+d+d+d+d+d+d+d \\
+h+i \\
+1 \\
\end{aligned}
\]

\[
\begin{aligned}
x &= a+b+c+ \\
& +d+e+f+g+d+d+d+d+d+d+d+d+d+d+d+d \\
& +h+i \\
& +1 \\
\end{aligned}
\]
```

效果：

![image-20220214001008550](https://s2.loli.net/2022/02/14/oZugGY2MF78Lk9t.png)

若想要公式自带编号，可以用 `gather` 和 `align` 环境，其中 `gather` 环境将公式分行渲染，`align` 同上述 `aligned`，可以控制对齐：

```latex
\begin{gather}
a = b+c+d \\
x = y+z \\ 
p = a_1 + a_2 + a_3 + \dots + a_{200}
\end{gather}

\begin{align}
a &= b+c+d \\
x &= y+z \\
p &= a_1 + a_2 + a_3 + \dots + a_{200}
\end{align}
```

![image-20220214001355079](https://s2.loli.net/2022/02/14/5tZcTngyKSIsWxV.png)

若想使用分段函数，可以使用 `cases` 环境：

```latex
\[
y= 
\begin{cases}
-x,\quad x\leq 0 \\
x,\quad x>0
\end{cases} 
\]
```

#### 小结

数学公式是在引入了 **amsmath** 包之后，利用其提供的各种各样次环境来实现了较为复杂的公式的编辑。整体来说，与 Mathjax 的风格相差不大，因此迁移学习起来也十分方便。

这里我们再提供辅助工具：

+ https://mathpix.com/ 能够 OCR 手写体或是印刷体公式，而后将图片中的公式转换成 LaTeX 数学公式的代码。

### 表格

`tabular` 环境提供了最简单的表格功能。它用 `\hline` 命令表示横线，在列格式中用 `|` 表示竖线；用 `&` 来分列，用 `\\` 来换行；每列可以采用居左、居中、居右等横向对齐方式，分别用 `l`、`c`、`r` 来表示。

```latex
\begin{tabular}{|l|c|r|}
 \hline
操作系统& 发行版& 编辑器\\
 \hline
Windows & MikTeX & TexMakerX \\
 \hline
Unix/Linux & teTeX & Kile \\
 \hline
Mac OS & MacTeX & TeXShop \\
 \hline
通用& TeX Live & TeXworks \\
 \hline
\end{tabular}
```

![img](https://s2.loli.net/2022/02/14/ytG5hReFbjWClwg.jpg)

### 图片

在 LaTeX 中插入图片，有很多种方式。最好用的应当属利用 `graphicx` 宏包提供的 `\includegraphics` 命令。比如你在你的 TeX 源文件同目录下，有名为 `a.jpg` 的图片，你可以用这样的方式将它插入到输出文档中：

```latex
\documentclass{article}
\usepackage{graphicx}
\begin{document}
\includegraphics{a.jpg}
\end{document}
```

想要了解更多，参见 `graphicx` 的文档：http://texdoc.net/texmf-dist/doc/latex/graphics/graphicx.pdf。

> **浮动体环境**
>
> 什么是浮动体环境：`table` 与 `figure`，两种浮动体环境可以替代上述的表格和图片环境，实现为表格或图片自动安排位置。
>
> 想了解更多有关浮动体环境的内容，详见[这里](https://liam.page/series/#LaTeX-%E4%B8%AD%E7%9A%84%E6%B5%AE%E5%8A%A8%E4%BD%93)。

### 页面设置

#### 页边距

设置页边距，推荐使用 `geometry` 宏包。可以在[这里](http://texdoc.net/texmf-dist/doc/latex/geometry/geometry.pdf)查看它的说明文档。

比如我希望，将纸张的长度设置为 20cm、宽度设置为 15cm、左边距 1cm、右边距 2cm、上边距 3cm、下边距 4cm，可以在导言区加上这样几行：

```
\usepackage{geometry}
\geometry{papersize={20cm,15cm}}
\geometry{left=1cm,right=2cm,top=3cm,bottom=4cm}
```

#### 页眉页脚

设置页眉页脚，推荐使用 `fancyhdr` 宏包。可以在[这里](http://texdoc.net/texmf-dist/doc/latex/fancyhdr/fancyhdr.pdf)查看它的说明文档。

比如我希望，设置自定义页眉；页脚的正中写上页码；页眉和正文之间有一道宽为 0.4pt 的横线分割，可以在导言区加上如下几行：

```latex
\usepackage{fancyhdr}
\pagestyle{fancy}
\lhead{页眉左侧}
\chead{页眉中间}
\rhead{页眉右侧}
\lfoot{}
\cfoot{\thepage}
\rfoot{}
\renewcommand{\headrulewidth}{0.4pt}
\renewcommand{\headwidth}{\textwidth}
\renewcommand{\footrulewidth}{0pt}
```

#### 段间距

我们可以通过修改长度 `\parskip` 的值来调整段间距。例如在导言区添加以下内容

```
\addtolength{\parskip}{.4em}
```

则可以在原有的基础上，增加段间距 0.4em。如果需要减小段间距，只需将该数值改为负值即可。

### 引用与尾注脚注

#### 交叉引用

交叉引用设置方法：

+ 给对象命名：`\label{name}`
+ 引用对象：`\ref{name}`

注意，在引用对象时，`\ref{name}` 会被替换会被引用对象的编号。举个例子，如果被引用对象在文档中是第 5 个被命名的，那么这里就会被替换为 5.

要想避免图/表/论文等等引用在计数上互相影响，你可以在命名时命名为 `tag:name` 的格式，引用时使用 `tag:name` 的格式来引用。具体来说，这些 tag 有：

| Tag           | Description          |
| ------------- | -------------------- |
| **`ch:`**     | chapter              |
| **`sec:`**    | section              |
| **`subsec:`** | subsection           |
| **`fig:`**    | figure               |
| **`tab:`**    | table                |
| **`eq:`**     | equation             |
| **`lst:`**    | code listing         |
| **`itm:`**    | enumerated list item |
| **`alg:`**    | algorithm            |
| **`app:`**    | appendix subsection  |

#### 尾注脚注

尾注直接在最后写就行，记得设置引用。

脚注可以使用 `\footnote{角注内容}` 来声明。

> 想了解该如何更好地引入参考文献，请学习 BibTeX 宏包。
>
> + https://zh.wikipedia.org/wiki/BibTeX

### 列表与枚举

```latex
\begin{enumerate}
    \item \LaTeX{} 好 处 都 有 啥
        \begin{description}
            \item[好 用] 体 验 好 才 是 真 的 好
            \item[好 看] 强 迫 症 的 福 音
            \item[开 源] 众 人 拾 柴 火 焰 高
        \end{description}
    \item 还 有 呢?
        \begin{itemize}
            \item 好 处 1
            \item 好 处 2
    \end{itemize}
\end{enumerate}
```

![image-20220214004408507](https://s2.loli.net/2022/02/14/U3E8uCBy4RKxfSL.png)

## LaTeX 后续学习

### 更多宏包

宏包一般都会提供相应的文档供我们阅读使用。

这里提供查询宏包对应文档的网站：

+ https://texdoc.org/index.html

![image-20220214003834121](https://s2.loli.net/2022/02/14/jkVbA8vBeY9FsUR.png)

### 制作自己的模板

详见参考资料中 `.cls` 文件详解部分。我们同时推荐读者可以去多读一读其他已存在的 Template 的 `.cls` 内容。

### 制作幻灯片

使用 Beamer 宏包可以制作幻灯片。详见：

+ https://www.overleaf.com/learn/latex/Beamer

同时，校内也提供了一些适用于各种 pre 的 Beamer 模板。

## 参考链接

+ （推荐）一份其实很短的 LaTeX 入门文档：https://liam.page/2014/09/08/latex-introduction/
+ （推荐）如何使用 LaTeX 排版论文：https://github.com/tuna/thulib-latex-talk
+ （`.cls` 文件详解）How to write a LaTeX class file and design your own CV： https://www.overleaf.com/learn/latex/How_to_write_a_LaTeX_class_file_and_design_your_own_CV_(Part_1)
