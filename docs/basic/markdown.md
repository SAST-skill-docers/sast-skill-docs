# Markdown 入门

!!! note "前言"

    Markdown 是一种学习成本较低的轻量级标记语言。它用简单的标记语法代替按钮，因此能省去频繁使用鼠标调整排版的麻烦，为文档编写者提供便利。用 Markdown 编写的内容可导出为 HTML、Word、PDF 等多种格式的文档。由于易读易写，它在 GitHub、简书等网站上也有广泛的应用。
    
    通过阅读本文档，读者可以在五分钟内快速学习到常用的 Markdown 标记。更多的内容请移步菜鸟教程。
    
    我们强烈建议计算机类的同学们在大一的春季学期学会 Markdown 或 LaTeX 二者中其中之一，因为这将是我们后续实验报告或大作业报告将利用的常用工具。
    
    在开始学习 Markdown 语法之前，建议先通过 [Typora 官网](https://typora.io/) 安装好 Typora 编辑器。

## 语法

### 标题

共有 1-6 级标题，使用 `#` 标记。`#` 数量与标题级别对应。注意 `#` 与文字间的空格不可省略。

```markdown
#### 四级标题
##### 五级标题
###### 六级标题
```

#### 四级标题
##### 五级标题
###### 六级标题

### 字体

```markdown
*斜体* _斜体_
**粗体** __粗体__
***粗斜体*** ___粗斜体___
~~删除线~~
<u>下划线</u>
```

*斜体* _斜体_

**粗体** __粗体__

***粗斜体*** ___粗斜体___

~~删除线~~

<u>下划线</u>

### 列表

无序列表用 `*` 或 `+` 或 `-` 标识，有序列表用数字和 `.` 来标识，支持列表嵌套。

```markdown
* 第一项
+ 第二项
- 第三项
    1. 子项一
        1. 孙子项一
            * 重孙子项一
```

* 第一项
+ 第二项
- 第三项
    1. 子项一
        1. 孙子项一
            * 重孙子项一

### 引用

```markdown
> 这是一条无意义的引用。
```

> 这是一条无意义的引用。

### 代码

```markdown
加法操作由 `add()` 函数完成
```

加法操作由 `add()` 函数完成

````markdown
```Python
def add(a, b):
	return a + b
```
````

```Python
def add(a, b):
	return a + b
```

### 链接

```markdown
[菜鸟教程](https://www.runoob.com/markdown/md-tutorial.html)
<https://www.runoob.com/markdown/md-tutorial.html>
```

[菜鸟教程](https://www.runoob.com/markdown/md-tutorial.html)

<https://www.runoob.com/markdown/md-tutorial.html>

### 图片

插入图片需依照 `![alt 属性文本](图片地址)` 格式。注意不要使用本地路径，可以将图片上传到清华云盘再获取图片链接。

```markdown
![示例图片](https://i.loli.net/2021/10/08/R3wUTtaAZHjSxEV.png)
```

![示例图片](https://i.loli.net/2021/10/08/R3wUTtaAZHjSxEV.png)

## 其他

Markdown 还有一些高级玩法，例如表格、公式、HTML 元素、画时序图、画流程图等等。由于不是很常用，这些都留给有兴趣的读者自行探索。

友情链接：

+ 菜鸟教程 <https://www.runoob.com/markdown/md-tutorial.html>
+ **（推荐学习）**公式编辑教程 <https://www.jianshu.com/p/25f0139637b7>
+ 绘制流程图、时序图、甘特图 <https://www.jianshu.com/p/6dbcc3aff98b>
