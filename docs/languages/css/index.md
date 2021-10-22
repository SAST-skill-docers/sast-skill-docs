# CSS 语言基础

## 样式与样式表

CSS 的全称为 **C**ascading **S**tyle **S**heets（层叠样式表），可以系统地描述网页内容的布局与面貌。

你可以点击<a href="/static/languages/css/0.html" target="blank">链接</a>来体会一下样式表的意义。在这个示例中，描述网页内容的 HTML 文本只有一份，但通过改变样式表，网页的布局就有了天翻地覆的变化。

!!! info "引入 CSS"

    在 HTML 中引入样式的方式通常有三种：

    - 内嵌于页面元素中；
    - 通过 `<style>` 元素嵌入网页；
    - 通过外部链接引用。

    在这份文档中，出于方便考虑，我们采用通过 `<style>` 元素嵌入网页的形式进行讲解。

**常见的样式有颜色、字体、边距、对齐、背景、边框等。**

我们先回到熟悉的 HTML：

```html
<body>
    <h1>这是标题</h1>
    <p>这是段落。和我一起读：</p>
    <p>你——好——世——界——</p>
    <p>
        <a href="https://www.baidu.com">前往百度</a>
    </p>
</body>
```

考虑这样一个需求：

- 将主体（`<body>`）背景设为淡蓝色（`lightblue`）；
- 将标题（`<h1>`）设为白色居中；
- 将正文（`<p>`）字号设为 `20px`；
- 将链接（`<a>`）颜色设为红色（`red`）。

为了实现这些样式层面的需求，样式表诞生了。我们先看示例，再来分析 CSS 的语法格式：

```css
body {
    background: lightblue;
}

h1 {
    color: white;
    text-align: center;
}

p {
    font-size: 20px;
}

a {
    color: red;
}
```

我相信，当你看到这样的文本时，已经能凭借直觉理解它的含义了。例如，`body { background: lightblue; }` 的含义就是将所有 `<body>` 元素的 `background` 设置为 `lightblue`。

CSS 的语法格式非常简单，如下图所示。也正式这样一系列简单的规则描述，相互组合之后就能描述丰富的网页排版样式。

![CSS 语法规则](https://www.runoob.com/wp-content/uploads/2013/07/632877C9-2462-41D6-BD0E-F7317E4C42AC.jpg)

我们再来看一下，将 CSS 引入 HTML 后的一个完整网页文件将会呈现怎样的结构：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="utf-8">
    <style>
        body { background: lightblue; }
        h1 { color: white; text-align: center; }
        p { font-size: 20px; }
        a { color: red; }
    </style>
    <title>CSS 示例</title>
</head>
<body>
<h1>这是标题</h1>
<p>这是段落。和我一起读：</p>
<p>你——好——世——界——</p>
<p>
    <a href="https://www.baidu.com">前往百度</a>
</p>
</body>
</html>
```

你可以点击链接 <a href="/static/languages/css/1-before.html" target="blank">引入 CSS 前</a> 以及 <a href="/static/languages/css/1-after.html" target="blank">引入 CSS 后</a> 来感受 CSS 的作用。

## 类

使用上述样式表，我们可以对网页中指定类型的元素设置统一的样式。然而，我们有时会希望只对某一部分元素设定样式，这时就要引入**类**的概念了。

类的 CSS 选择语法与元素的类似，只需在类名前加上一个 `.` 即可。例如，我们希望定义一个类 `red`，它的背景色是红色（`red`），文本颜色为白色，且具有 `10px` 的内边距（`padding`）和 `5px` 的外边距（`margin`）。我们可以这样写：

```css
.red {
    background: red;
    color: white;
    padding: 10px;
    margin: 5px;
}
```

在 HTML 代码中，我们只需给一个元素赋予 `class="red"` 的属性，这个元素就具备了我们所定义的 `red` 类的样式：

```html
<div class="red">
    <p>红色主题</p>
</div>
```

!!! tip "使用 `<div>` 添加分组"

    当我们想要将一系列元素视为一个整体进行布局时，我们可以采用 `<div>` 元素创建一个分组，对这个分组设置样式或进行排版。

我们还可以以此类推，创建 `blue`、`green` 类。完整代码如下所示：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="utf-8">
    <style>
        .red {
            background: red;
            color: white;
            padding: 10px;
            margin: 5px;
        }

        .blue {
            background: blue;
            color: white;
            padding: 10px;
            margin: 5px;
        }

        .green {
            background: green;
            color: white;
            padding: 10px;
            margin: 5px;
        }
    </style>
    <title>CSS 示例</title>
</head>
<body>
    <div class="red">
        <p>红色主题</p>
    </div>
    <div class="blue">
        <p>蓝色主题</p>
    </div>
    <div class="green">
        <p>绿色主题</p>
    </div>
</body>
</html>
```

你可以点击<a href="/static/languages/css/2.html" target="blank">链接</a>在浏览器中预览这段代码。

观察上述代码，我们发现，`red`、`blue`、`green` 类的 CSS 定义中都有三条重复的属性。我们可以将公共部分提取为一个公共的类 `theme`，并给 HTML 中的每个 `<div>` 元素的 `class` 属性都额外添加一个 `theme` 类，如下所示：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="utf-8">
    <style>
        .theme {
            color: white;
            padding: 10px;
            margin: 5px;
        }

        .red {
            background: red;
        }

        .blue {
            background: blue;
        }

        .green {
            background: green;
        }
    </style>
    <title>CSS 示例</title>
</head>
<body>
    <div class="red theme">
        <p>红色主题</p>
    </div>
    <div class="blue theme">
        <p>蓝色主题</p>
    </div>
    <div class="green theme">
        <p>绿色主题</p>
    </div>
</body>
</html>
```

这样，我们的 CSS 代码也变得更加清晰与简洁。

!!! tip "用好开发者工具"

    Firefox、Chrome、Edge、Safari 等浏览器均为开发者提供了用于查看和调试网页的工具。打开开发者工具的快捷键通常为 F12，当然这也可能因你的浏览器和操作系统而异。你可以借助搜索引擎进一步了解。

    以上述示例代码的网页为例，点击<a href="/static/languages/css/2.html" target="blank">链接</a>，在打开的页面中启动开发者工具，动手查看一下元素的组织方式、元素的样式以及它们所属的类吧！

    以下是使用 Firefox 浏览器调试上述示例代码网页的截屏。

    ![Firefox-debug.png](https://i.loli.net/2021/10/22/ImOGY54eTNnrpFX.png)

    你也可以利用同样的方式对后续的示例网页进行查看与调试。

## 组合选择器

考虑这样的需求：只希望 `theme` 类下的链接变为红色，其余位置的链接仍需使用默认样式。这就需要用到 CSS 组合选择器的语法了。如下所示：

```css
.theme a {
    color: red;
}
```

`.theme` 是类选择器，`a` 是元素选择器，它们之间通过空格 **` `** 分隔，表示只对 `.theme` 类的 `<a>` 元素后代有效。

完整代码如下所示：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="utf-8">
    <style>
        .theme {
            color: white;
            background: darkred;
            padding: 20px;
            margin: 10px;
        }

        .theme a {
            color: red;
        }
    </style>
    <title>CSS 示例</title>
</head>
<body>
<div class="theme">
    <p>红色主题，<a href="https://www.baidu.com">红色链接</a></p>
</div>
<p><a href="https://www.baidu.com">普通链接</a></p>
</body>
</html>

```

你可以点击<a href="/static/languages/css/3.html" target="blank">链接</a>在浏览器中预览这段代码。

!!! info "不同的组合方式"

    在 CSS3 中一共包含了四种组合方式：

    - 后代选择器(以空格 **` `** 分隔)
    - 子元素选择器(以大于号 **`>`** 分隔）
    - 相邻兄弟选择器（以加号 **`+`** 分隔）
    - 普通兄弟选择器（以波浪号 **`～`** 分隔）


!!! info "参考手册"

    CSS 选择器有多种多样的表达形式，你可以参阅[CSS 选择器参考手册](https://www.w3school.com.cn/cssref/css_selectors.ASP)来找到你所需要的选择器语法。

## 盒模型

![盒模型](https://www.runoob.com/images/box-model.gif)

盒模型将每个 HTML 元素视为一个方形盒子，关注的核心在于内外边距以及边框的样式。在排版布局时，我们需要合理设置 `padding` 和 `margin` 的数值，才能让自己的网页呈现出最舒适的布局。

## 网页布局

![网页布局](https://www.runoob.com/wp-content/uploads/2019/04/DBD1E737-47C5-445E-BFEC-7547210D88D5.jpg)

网页的布局通常有一些固定的模式。我们一般会将各个区域用 `<div>` 元素组合起来，然后考虑这些区域的排版布局。

!!! tip "了解原理即可"

    事实上，我们很少会完全手写网页的布局，因为许多前端样式库能够帮我们实现这些事情，且通常比我们手写的做得更好。

### 左右布局

网页的排布逻辑一般都是自上而下的。左右布局的区域通常需要组合后嵌套在一行中，且需要利用浮动（`float`）属性，如下所示：

```html
<div>上面的内容</div>
<div>
    <div style="width: 25%; float: left;">左</div>
    <div style="width: 50%; float: left;">中</div>
    <div style="width: 25%; float: right;">右</div>
</div>
<div>下面的内容</div>
```

!!! info "内嵌样式"

    你可能注意到了，上面的示例代码中，左边区域和右边区域的 `<div>` 元素上都添加了 `width`、`float` 等样式属性。

    这是一种通过内嵌于页面元素引入 CSS 的方式，可以“临时”“一次性”地为一些元素添加样式。

### 固定布局

我们有时会希望一些区域固定在网页的某个位置，例如我们会希望导航栏固定在页面顶部，不随页面滚动而改变。这时，我们只需使用 `position: fixed` 属性，如下所示：

```css
.top {
    position: fixed;
    top: 0;
    left: 0;
}
```

即可让 `top` 类的元素实现固定顶部的效果。

完整代码如下所示：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="utf-8">
    <style>
        body {
            margin: 0;
        }

        .top {
            position: fixed;
            top: 0;
            left: 0;
            height: 50px;
            width: 100%;
            background: red;
        }

        .main {
            margin-top: 50px;
        }

        .left {
            width: 25%;
            background: lightgray;
            height: 200px;
            float: left;
        }

        .center {
            width: 50%;
            background: lightgreen;
            height: 2000px;
            float: left;
        }

        .right {
            width: 25%;
            background: lightgray;
            height: 200px;
            float: right;
        }

        .footer {
            height: 50px;
            width: 100%;
            background: green;
        }

        .row:after {
            content: "";
            display: table;
            clear: both;
        }
    </style>
    <title>CSS 示例</title>
</head>
<body>
<div class="top"></div>
<div class="main">
    <div class="row">
        <div class="left"></div>
        <div class="center"></div>
        <div class="right"></div>
    </div>
    <div class="footer"></div>
</div>
</body>
</html>
```

你可以点击<a href="/static/languages/css/4.html" target="blank">链接</a>在浏览器中预览这段代码。

## 后续拓展

现在，你已经学会了通过 CSS 控制网页的样式。接下来，你可以为你的网页赋予行为，这就需要 JavaScript 的相关知识。

当然，你可能会不满于手写 CSS 样式，想要探索一些前端样式库，因此你也可以继续往下阅读前端样式库的有关文档。

后续可以阅读的文档有：

- JavaScript 语言基础 [https://docs.net9.org/languages/javascript](https://docs.net9.org/languages/javascript)
- Bootstrap 简介 [https://docs.net9.org/languages/css/bootstrap](https://docs.net9.org/languages/css/bootstrap)
- MDUI 简介 [https://docs.net9.org/languages/css/mdui](https://docs.net9.org/languages/css/mdui)

## 资源链接

- w3school CSS 教程 [https://www.w3school.com.cn/css/index.asp](https://www.w3school.com.cn/css/index.asp)
- 菜鸟教程 CSS 教程 [https://www.runoob.com/css/css-tutorial.html](https://www.runoob.com/css/css-tutorial.html)
