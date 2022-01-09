# 浏览器中的 JavaScript

JavaScript 最初设计的目的就是运行在浏览器中给静态 HTML 赋予动态效果，而即使经历了若干年代，现在的浏览器中通用的脚本语言依然是 JavaScript。

即使你使用前端框架搭建网页应用，我们依然建议你阅读这一部分内容。这是因为前端框架最终依然是将你的代码翻译为浏览器中的 JavaScript 并运行，掌握这一部分内容可以帮助你更好地理解框架运作流程和 debug。

## 浏览器内置对象

浏览器内置了部分全局对象，这些对象往往与窗口、屏幕、浏览记录等信息有关。编写相关 JavaScript 代码的时候可以通过这些对象获取相关信息，也可以通过这些调用对象的方法来实现一些和浏览器相关的操作。

`window` 对象是最常用的内置全局变量之一。该变量的一大重要作用是**充当全局作用域**，也就是将所有全局变量和函数绑定为其属性，这一点在先前讲解 `this` 指向的时候提到过，参见 [JavaScript 的面向对象](/languages/javascript/oop) 部分的“全局对象”注解。

除此之外，`window` 对象有 `innerHeight, innerWidth, outerHeight, outerWidth` 等与窗口尺寸有关的属性，比如你可以在浏览器控制台中运行下述代码获取浏览器内窗口的宽高：

```javascript
console.log(`Inner window size: ${window.innerWidth} x ${window.innerHeight}`);
```

你可以尝试最大化或拖动缩放浏览器窗口后再运行上述代码，观察输出的改变。

`screen` 对象保存的则是屏幕相关的信息，如屏幕宽高和颜色位数等：

```javascript
console.log(`Screen size: ${screen.width} x ${screen.height}`);
```

`navigator` 对象保存了浏览器相关的大量信息，比如说浏览器名称、语言、版本、操作系统类型等等：

```javascript
console.log(`{
    name: ${navigator.appName},
    version: ${navigator.appVersion},
    language: ${navigator.language},
    platform: ${navigator.platform},
    userAgent: ${navigator.userAgent},
}`);
```

`location` 对象保存了和 URL 相关的一些信息。假设当前页面的 URL 为下述，其包含了协议、主机名、端口号、参数等等信息：

```markdown
https://www.test.com:8000/static/path?key=114514
```

此时 `location` 对象中的各个属性存储着这个 URL 的各部分：

- `location.href` 存储 URL 本身。即 `https://www.test.com:8000/static/path?key=114514`
- `location.protocol` 存储 URL 使用的协议。即 `https`
- `location.host` 存储当前的主机名。即 `www.host.com`
- `location.port` 存储当前使用的端口。即 `8000`
- `location.pathname` 存储当前访问的资源路径。即 `/static/path`
- `location.search` 存储当前传递的参数列表。即 `?key=114514`，注意 `?` 字符也包含在内

`location` 对象还有两个常用方法，即 `location.assign` 和 `location.reload`。前者用于强制页面跳转，后者则用于强制重新载入当前页面。读者可以自行尝试在控制台中运行下述代码：

```javascript
location.assign("https://docs.net9.org");
location.reload();
```

`document` 对象是当前页面整个 DOM 树的根节点。

!!! note "什么是 DOM 树"

    HTML 所表示的页面中各个元素是按照树的结构安排的，树上的每一个节点都是一个 HTML 元素，而这棵树就是所谓的 DOM 树（英语：Document Object Module Tree）。

    相关知识可以参考 [HTML 语言基础](/languages/html)。

在后续部分我们会具体介绍该如何利用好 `document` 对象来查询、修改 DOM 树。

## DOM 树节点常用方法

!!! caution "很多时候你不需要直接操作 DOM 树"

    JavaScript 语言本身开放了相当多且功能强劲的函数以操作 DOM 树，但是如果利用不当，很有可能造成网页崩溃等意料之外的结果。为了避免这种情况以及方便编程人员，许多网页前端框架已经将对 DOM 树的操作封装成相关的函数或者语法。

    如果你只需要学习具体的框架以完成特定的前端项目，请尽量减少对 DOM 树的直接操作。但是为了构建完整的 JavaScript 逻辑体系，我们还是建议至少了解、知晓这些 DOM 树语法。

!!! note "使用我们提供的样例"

    在学习这一小节的时候，可以点击进入 [我们的样例网页](/static/languages/javascript/dom-html.html) 并按下 F12 打开控制台，在控制台里运行这一节提供的样例代码来理解 DOM 树操作。



### 查找 DOM 节点

我们可以使用下述内置方法查找符合要求的 DOM 节点：

- `document.getElementById`。根据节点 ID 查找，节点 ID 是唯一的，故该方法返回的是具体的 DOM 节点。比如说：

```javascript
document.getElementById("red-block");
```

上述代码会返回一个 DOM 元素，在控制台里显示为一个 HTML 标签，鼠标悬浮于标签上可以看到网页上对应元素高亮显示。

- `getElementsByClassName`。该方法为 DOM 节点对象内置方法，含义为在以该节点为根的子树内查找所有 `class` 属性为给定值的节点，其返回的是一个 DOM 节点的列表。比如说：

```javascript
document.getElementsByClassName("block");
```

该方法返回了所有 `class` 为 `square` 的 DOM 节点构成的列表。

上述两个方法可以联合使用：

```javascript
document.getElementById("circle-wrapper").getElementsByClassName("circle");
```

这段代码意味着先根据 ID 查找到一个 DOM 节点，再在其内部查找所有 `class` 属性为 `circle` 的节点。

- `getElementsByTagName`。该方法为 DOM 节点对象内置方法，含义为在以该节点为根的子树内查找所有标签名为给定值的节点（如 `<p />` 节点的标签名就是 `'p'`），其返回的是一个 DOM 节点的列表。比如说：

```javascript
document.getElementById("circle-wrapper").getElementsByTagName("div");
```

---

除此以外，我们还可以使用 DOM 节点对象内置的 `querySelector` 与 `querySelectorAll` 方法，利用选择器语法来进行更精确的查找。

`querySelector` 搜索范围是以该 DOM 节点为根的子树中满足选择器要求的**第一个** DOM 节点。而 `querySelectorAll` 会返回子树内所有满足选择器要求的 DOM 节点构成的列表。

具体的选择器语法可以参考 [CSS 语言基础文档中相关部分](/languages/css#_3)。

### 更新 DOM 树节点

!!! note "边做边学"

    这一节中，读者可以先在控制台中运行：

    ```javascript
    let node = document.getElementById("test-text");
    ```

    来获取我们用于演示的 DOM 节点。

DOM 节点对象有一个相当重要的属性，即 `innerHTML`，其值就是一对闭合的 HTML 标签之间的文本。比如说下述 HTML 的 `<p>` 节点：

```html
<p> The color is <span style="color:red"> RED </span> ! </p>
```

其 `innerHTML` 属性值就是 `' The color is <span style="color:red"> RED </span> ! '`。

这个属性是可以随意读写的，所以我们完全可以通过修改这个属性值来直接调整 DOM 节点。比如说在控制台中运行下述代码：

```javascript
node.innerHTML = ' The color is <span style="color:red"> RED </span> ! '
```

就会发现原先的文本 `This is a test text node.` 替换成了我们设置的文本。

!!! caution "XSS 攻击"

    你可能已经意识到了，赋给 `innerHTML` 属性的字符串值中可以包括新的 HTML 节点，这意味着通过编写特定的字符串值，我们可以给 DOM 树插入新节点，甚至通过 `<script>` 标签引入恶意 JavaScript 代码。

    这也就是我们需要注意的 XSS 攻击。防范这种攻击的最简单的方法就是，严格控制 `innerHTML` 属性的赋值（最好不手动给这个属性赋值，尤其是给其赋予从网络上获取的字符串值）或者使用 `innerText` 属性。

另外一个常用的属性是 `innerText`，其和 `innerHTML` 属性基本类似，但是不同之处是其会进行字符转义，比如说 `<` 字符会转义为 `&lt;`，这样就保证了字符串就是字符串，不会被解读为新 DOM 节点。

此外，我们还可以修改节点的样式，只需要修改其 `style` 属性即可。不过注意，CSS 中允许属性名中包含短划线 `-`，但是 JavaScript 中不允许，所以遇到这类属性名时，请使用驼峰命名法转写：

```javascript
node.style.color = "red";
node.style.fontSize = "20px"; // "font-size" in CSS
```

### 调整 DOM 树结构

我们可以通过 DOM 节点对象的 `appendChild` 方法来为该节点增添一个子节点。不过注意，如果增添的子节点是原先 DOM 树上具有的节点，则首先会将这个节点摘除后添加到指定的位置。

在控制台中运行以下代码：

```javascript
let wrapper = document.getElementById("circle-wrapper");
let redCircle = document.getElementById("red-circle");
wrapper.appendChild(redCircle);
```

可以发现三个圆形调换了位置，红色的圆形成为了位于最底部的圆形。

但是我们可以发现 `appendChild` 方法永远将该节点插入为指定节点的最后一个子节点，如果我们需要具体指定插入位置，就需要使用 `insertBefore` 方法，该方法需要指定节点的一个子节点作为参考子节点：

```javascript
let wrapper = document.getElementById("circle-wrapper");
let redCircle = document.getElementById("red-circle");
let blueCircle = document.getElementById("blue-circle");
wrapper.insertBefore(redCircle, blueCircle); // The 2nd param is the reference node
```

这样，红色的圆形就插入在蓝色的圆形之前。

---

有时我们需要给 DOM 树增添节点，这个时候就需要使用 `document.createElement` 方法，该方法能创造一个指定类型的 DOM 树节点。随后我们可以设置其各种属性值，最后可以使用各种插入方法将这个新节点插入到 DOM 树中。

```javascript
let purpleCircle = document.createElement("div");
purpleCircle.id = "purple-circle";
purpleCircle.className = "circle";
purpleCircle.style.backgroundColor = "purple";

let wrapper = document.getElementById("circle-wrapper");
wrapper.appendChild(purpleCircle);
```