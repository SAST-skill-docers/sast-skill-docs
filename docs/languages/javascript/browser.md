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

    在学习这一小节的时候，可以点击进入 [我们的样例网页](/static/languages/javascript/dom-html.html) 并按下 ++f12++ 打开控制台，在控制台里运行这一节提供的样例代码来理解 DOM 树操作。


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

---

删除某一个节点则较为简单，我们只需要获取需要删除的节点的引用以及其父节点的引用，然后在父结点上调用 `removeChild` 方法即可删除。参考以下代码：

```javascript
let wrapper = document.getElementById("circle-wrapper");
let redCircle = document.getElementById("red-circle");
let removedCircle = wrapper.removeChild(redCircle);

console.log(redCircle === removedCircle); // true
```

可以发现，`removedChild` 方法具有返回值，其返回值是删除掉的节点的引用。

## jQuery

你或许听闻过 jQuery 这个第三方库，其如此流行的原因是其简化了 JavaScript 语言操作 DOM 树、修改 CSS 的语法，并且消除浏览器差异，可以将统一的语法运用于任何一个主流浏览器。

!!! note "Web 前端的更新换代"

    最初的前端是没有 jQuery 的，所以都需要使用 `getElementById` 等 DOM 节点内置方法来完成 DOM 树操作。而随后，jQuery 出现，这种跨浏览器且语法简洁的第三方库立刻得到了广泛运用。

    但是近年来随着前端框架流行，编写裸 JavaScript 的机会越来越少，修改 DOM 树等操作也在前端框架内被封装为各种框架方法，jQuery 也渐渐淡出。但是由于历史原因，我们现在还是能看到大量的需要手写裸 JavaScript 的前端，这些前端往往应用了 jQuery。所以，学习 jQuery 的基础知识还是有一定的必要。

我们提供的 [样例网页](/static/languages/javascript/dom-html.html) 已经为你引用了 jQuery，可以运行下述代码来确定是否引用成功：

```javascript
console.log($.fn.jquery); // 2.1.4
```

这里 `$` 就是 jQuery 的全局封装对象，其提供的所有功能全都是 `$` 的成员函数实现的。

### 选择器

具体的选择器语法可以参考 [CSS 语言基础文档中相关部分](/languages/css#_3)。

我们可以在 jQuery 中使用选择器语法来获取 DOM 树节点，注意通过下述方法获取的是一个 DOM 树节点的列表，如果没有符合选择器要求的节点则返回空列表。如：

```javascript
let wrapper = $("#circle-wrapper"); // Find nodes with 'id=circle-wrapper'
console.log(wrapper.length); // 1
console.log(wrapper[0]);
```

除了按照 ID 查找，下面还举出了一些常用的选择器：

```javascript
let circles = $(".circle"); // Find nodes with 'class=circle'
console.log(circles);

let divs = $("div"); // Find nodes which are <div>
console.log(divs);
```

可以注意到这里输出的列表并不是我们之前见过的 DOM 树节点。实际上，其返回的是包装过一层的 jQuery 对象。如果需要得到最基本的 DOM 树节点对象，可以使用下标运算符 `[]` 取出具体的元素或者使用 `.get()` 方法。

```javascript
let circles = $(".circle"); // Find nodes with 'class=circle'
console.log(circles);
console.log(circles[0]);
console.log(circles.get(0));
```

### 修改 DOM 树

在获取到 DOM 树节点后，如果需要修改这个节点的内容，我们可以使用 `.text()` 或者 `.html()` 方法。这两个方法的共同点是如果无参数调用，则是获取这个节点的内容。如果有参数调用，则是将节点的内容修改为传入的参数。

`.text()` 与 `.html()` 分别对应着 `innerText` 和 `innerHTML` 属性。

```javascript
$("#test-text").text(); // "This is a test text node."
$("#test-text").text("Hello world!");
```

此外，我们还可以修改某个节点的 CSS：

```javascript
$("#red-circle").css("background-color", "black");
```

`.css()` 不能完全无参调用，至少需要在第一个参数中传递 CSS 属性名。如果第二个参数不传入，则表示获取这个节点某个 CSS 属性的值。如果第二个参数是空字符串，则表示删去这个 CSS 属性。

```javascript
$("#red-circle").css("background-color"); // "rgb(255, 0, 0)"
```

我们之前提过，使用选择器获取的是 DOM 树节点的列表而非单个节点。所以如果在含有多个 DOM 树节点的 jQuery 对象上使用 `.text()` 等方法修改内容，该修改会对这个列表内所有的 DOM 树节点生效。

比如我想把三个圆都变为黑色，就可以：

```javascript
$(".circle").css("background-color", "black");
```

---

除去 `.text()`、`.html()` 以及 `.css()`，获取其余的 HTML 标签属性可以使用 `.attr()`，这里可以参考 [廖雪峰的教程](https://www.liaoxuefeng.com/wiki/1022910821149312/1023023660400160)。

---

增添和删除节点也是简单的。增添节点只需要对父节点调用 `.append()` 方法，删除则是对要删除的节点调用 `.remove()` 方法：

```javascript
$("#circle-wrapper").append($("#red-circle"));
$("#red-circle").remove();
```

要在指定节点的前面或后面增添兄弟节点，则可以使用 `.after()` 或者 `.before()` 方法：

```javascript
$("#red-circle").after(
    $(document.createElement("div"))
        .attr("class", "circle")
        .css("background-color", "purple")
)
```

可以注意到这里将一个 DOM 树节点对象传入了 `$()` 之中，其作用是将 DOM 树节点包装为 jQuery 对象以使用 `.attr()` 等方法。

### 事件

我们现在考虑网页如何与用户交互，也就是说用户是如何触发描述交互行为的 JavaScript 的。这里，就需要引入**事件**这个概念。

用户点击某一个 HTML 组件或者在文本框中输入、文档树加载等行为都可以是事件，这些事件的相关信息（比如鼠标点击事件中鼠标点击位置的横纵坐标等）将会被包装为一个对象传入到 JavaScript 的事件处理循环，JavaScript 引擎接受到事件后就会调用相应的回调函数，而交互行为就定义在这些回调函数之中。

现在就以按钮点击事件为例，介绍如何利用 jQuery 编写点击事件的回调函数。

!!! note "使用我们提供的样例"

    在学习这一小节的时候，可以点击进入 [我们的样例网页](/static/languages/javascript/event-1.html) 并按下 ++f12++ 打开控制台，在控制台里运行这一节提供的样例代码来理解 DOM 树操作。

我们现在希望我们点击这个按钮后，网页能弹窗显示对我们的问候。显示弹窗的回调函数可以规定为：

```javascript
() => {
    alert("Hello!");
}
```

我们可以这样将上述回调函数绑定到按钮上：

```javascript
$("#test-button").on("click", () => {
    alert("Hello!");
});
```

这里 `$("#test-button")` 是先前提到过的选择器，用于选择出需要绑定事件回调函数的按钮。之后调用其 `.on()` 方法，第一个参数指示需要给何种事件绑定回调，这里 `"click"` 表示给点击事件绑定。第二个参数则是需要执行的回调函数，在这里，按钮被点击的时候，该回调就会执行。

由于点击事件很常用，所以还有这样的简写：

```javascript
$("#test-button").click(() => {
    alert("Hello!");
});
```

我们先前提到了，和事件相关的所有参数都会被包装为对象传入处理引擎，而这个对象就会进一步作为回调函数的参数传入，我们可以将其打印出来：

```javascript
$("#test-button").click(console.log);
```

这个时候点击按钮就可以看到控制台上输出的事件相关参数了。

---

但是如果我们需要真正自己编写一个有交互行为的网页，我们就需要把上面提到的代码嵌入 HTML 之中，因为我们要求这些交互行为随着网页加载就定义好，否则就需要用户手动在网页加载后在控制台定义，这显然违反常理。

你可能已经了解，HTML 中可以使用 `<script />` 标签插入 JavaScript 代码，所以你可能打算这样写：

```html
<!DOCTYPE html>
<html lang="zh">
    <head>
        <meta charset="utf-8">
        <title>按钮点击事件演示网页</title>
        <script src="//code.jquery.com/jquery-2.1.4.min.js"></script>
        <script>
            $("#test-button").on("click", () => {
                alert("Hello!");
            });
            console.log($("#test-button").length);
        </script>
    </head>
    <body>
        <button id="test-button">Click me!</button>
    </body>
</html>
```

上述 HTML 代码已经放在 [我们的样例网页之中](/static/languages/javascript/event-2.html)。如果你浏览后点击按钮，你会发现并没有按照预期弹出弹窗。

这里就需要提到 `<script />` 标签中代码的执行时机。实际上，这些代码将会在文档加载完毕之前就执行完毕，此时的 DOM 上还未有按钮对象，也就是说，选择器应该什么都获得不到，从而绑定失败。

实际上上述代码里，我们留下了一句 `console.log($("#test-button").length)`，所以可以在控制台里发现，此时选择器什么都没有选择到（输出为 `0`），符合我们的讲解。

那么如何解决这个问题呢，事实上我们先前提到过 DOM 加载完毕也是一个事件，所以我们可以将绑定按钮点击事件写为 DOM 加载完毕的回调：

```javascript
$(document).on("ready", () => {
    $("#test-button").on("click", () => {
        alert("Hello!");
    });
});
```

由于在 DOM 加载完毕后挂载各种回调函数是相当常见的需求，所以上述写法一般可以简化为：

```javascript
$(() => {
    $("#test-button").on("click", () => {
        alert("Hello!");
    });
});
```

这样修改后的代码可以参见 [我们的样例网页](/static/languages/javascript/event-3.html)。