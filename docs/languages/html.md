# HTML 语言基础

## 初始 HTML

HTML 的全称为 **H**yper **T**ext **M**arkup **L**anguage（超文本标记语言），是一种用于描述网页的标记语言。 互联网上五彩缤纷的网页就是使用 HTML 进行描述的。

这是最简单的 HTML 文本：

```html
<body>
    <h1>这是标题</h1>
    <p>这是段落。和我一起读：</p>
    <p>你——好——世——界——</p>
</body>
```

你可以点击<a href="/static/languages/html/0.html" target="blank">链接</a>在浏览器中预览这段 HTML 文本。

不难发现，HTML 文本由层级结构组成。每一级内容被一对 HTML 标签包围，构成一个 **HTML 元素**。元素之间**可以嵌套**。

```html
<标签>内容</标签>
```

在这段最简单的 HTML 文本中，我们已经见到了三种不同的 HTML 元素：

- `<body>` 元素表示网页的主体内容，用来容纳网页上所有可见元素；
- `<h1>` 元素表示一级标题；
- `<p>` 元素表示段落。

当然，这段最简单的 HTML 文本并不足以描述一个完整的网页。我们来看下面的示例：

```html
<!DOCTYPE html>
<html lang="zh">
    <head>
        <meta charset="utf-8">
        <title>HTML 示例</title>
    </head>
    <body>
        <h1>这是标题</h1>
        <p>这是段落。和我一起读：</p>
        <p>你——好——世——界——</p>
    </body>
</html>
```

你可以在自己的电脑上新建一个文件 `index.html`，将上面这段文本粘贴进去，保存后使用浏览器打开 `index.html`。

相比于最简单的 HTML 文本，这段完整的 HTML 网页多出了以下元素：

- `<!DOCTYPE html>` 用于表示这是一段 HTML 文本；
- `<html>` 元素是一个完整的 HTML 网页的根元素；
- `<head>` 元素用来容纳这个完整的 HTML 网页的一些元信息（meta information）；
- `<meta>` 元素用来表示这个完整的 HTML 网页的元信息；
- `<title>` 元素用来表示这个网页的标题。

这些元素看起来复杂，但它们是每个 HTML 网页的固定组成部分。你可以将它们视为一套固定的框架，当你想要书写新的 HTML 页面时，只需填写 `<body>` 元素的内容即可。

!!! tip "学会模仿"

    HTML 并不需要刻意学习。观察示例，理解 HTML 的结构，具备基本的阅读能力，能够动手魔改、仿写简单的页面即可。

## 典型的 HTML 元素与属性

HTML 可以满足绝大多数文本排版需求。

```html
<body>
    <p>HTML 文本具有丰富的格式，如<b>加粗</b>、<i>斜体</i>、<del>删除</del>。</p>
    <p>标签之间可以嵌套，<b>以表达<i>更为<del>花哨</del>多样</i>的格式</b>。</p>
    <p>你还可以在 HTML 文本中插入<a href="https://www.baidu.com">链接</a>。</p>
</body>
```

你可以点击<a href="/static/languages/html/1.html" target="blank">链接</a>在浏览器中预览这段 HTML 文本。

!!! info "元素的属性"

    在上面的示例中，元素 `<a>` 较为特殊。从形式上看，它多出一个 `href="..."`，用来描述该链接的目标地址。这里的 `html` 称为元素 `<a>` 的属性。 

HTML 可以描述有序和无序列表。

```html
<body>
    <p>这是 HTML 无序列表：</p>
    <ul>
        <li>第一项</li>
        <li>第二项</li>
        <li>第三项</li>
    </ul>
    <p>这是 HTML 有序列表：</p>
    <ol>
        <li>第一项</li>
        <li>第二项</li>
        <li>第三项</li>
    </ol>
</body>
```

你可以点击<a href="/static/languages/html/2.html" target="blank">链接</a>在浏览器中预览这段 HTML 文本。

你可以在 HTML 文本中添加互联网上的图片。

```html
<body>
    <p>你也可以添加图片。</p>
    <img src="替换为你的图片地址" alt="Loading..." />
</body>
```

你可以点击<a href="/static/languages/html/3.html" target="blank">链接</a>在浏览器中预览这段 HTML 文本。

你可以在 HTML 文本中插入表格。

```html
<body>
    <table>
        <tr>
            <td>(1, 1)</td>
            <td>(1, 2)</td>
        </tr>
        <tr>
            <td>(2, 1)</td>
            <td>(2, 2)</td>
        </tr>
    </table>
</body>
```

你可以点击<a href="/static/languages/html/4.html" target="blank">链接</a>在浏览器中预览这段 HTML 文本。

阅读学习了上述示例代码后，我们可以总结一下我们所学到的典型的 HTML 元素与属性。

|   名称   |    含义    |                属性                |
| :------: | :--------: | :--------------------------------: |
|  `<p>`   |    段落    |                 —                  |
|  `<h1>`  |  一级标题  |                 —                  |
|  `<h2>`  |  二级标题  |                 —                  |
| `<div>`  |    分组    |                 —                  |
|  `<a>`   |    链接    |          href="链接目标"           |
| `<img>`  |    图片    | src="图片地址"<br />alt="占位文本" |
|  `<b>`   |    粗体    |                 —                  |
|  `<i>`   |    斜体    |                 —                  |
| `<br />` | 段落内换行 |                 —                  |
| `<table>`|    表格    |                 —                  |
|  `<tr>`  |   表格行   |                 —                  |
|  `<td>`  |  表格元素  |                 —                  |

当然，在实际开发中，我们很可能用到更多丰富多样的 HTML 元素。这篇文档无意于穷举所有的 HTML 元素。当你需要查找某个特定的 HTML 元素的用法时，可以借助搜索引擎，或者参见这份文档最后列出的资源链接。

## 后续拓展

现在，你已经学会了基本的 HTML 文本的编写。在 Web 开发中，HTML 通常用于描述网页的内容。至于页面的布局和页面的动态行为，则需要 CSS 和 JavaScript 的辅助。

后续可以阅读的文档有：

- CSS 语言基础 [https://docs.net9.org/languages/css](https://docs.net9.org/languages/css)
- JavaScript 语言基础 [https://docs.net9.org/languages/javascript](https://docs.net9.org/languages/javascript)

## 资源链接

- w3school HTML 教程 [https://www.w3school.com.cn/html/index.asp](https://www.w3school.com.cn/html/index.asp)
- 菜鸟教程 HTML 教程 [https://www.runoob.com/html/html-tutorial.html](https://www.runoob.com/html/html-tutorial.html)
