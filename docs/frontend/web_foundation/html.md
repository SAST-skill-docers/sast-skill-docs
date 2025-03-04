## HTML

### 1. HTML（HyperText Markup Language）

#### **1.1 HTML的定义与作用**
HTML是用于构建网页结构的标记语言。它通过一系列的标签（Tags）定义网页的内容和结构，告诉浏览器如何显示页面内容。HTML文件以`.html`为扩展名，通常包含以下基本结构：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document Title</title>
</head>
<body>
    <h1>Welcome to My Web Page</h1>
    <p>This is a paragraph of text.</p>
</body>
</html>
```

#### **1.2 HTML文档结构**
1. **`<!DOCTYPE html>`**：
   - 声明文档类型，告诉浏览器这是一个HTML5文档。
2. **`<html>`**：
   - 根元素，包含整个HTML文档的内容。
   - `lang`属性指定文档的语言（如`en`表示英语）。
3. **`<head>`**：
   - 包含文档的元数据（Metadata），这些内容不会直接显示在页面上。
   - 常见的元数据包括：
     - **`<meta charset="UTF-8">`**：指定字符编码为UTF-8。
     - **`<meta name="viewport" content="width=device-width, initial-scale=1.0">`**：优化移动设备的显示效果。
     - **`<title>`**：定义文档的标题，显示在浏览器的标题栏或标签页上。
     - **`<link>`**：用于引入外部资源，如CSS文件。
     - **`<script>`**：用于引入外部JavaScript文件或定义内联脚本。
4. **`<body>`**：
   - 包含用户可见的内容，如文本、图像、按钮等。

#### **1.3 常用HTML标签**
HTML标签分为块级标签（Block-level）和行内标签（Inline-level）。

##### **1.3.1 文本标签**
- **标题标签**：`<h1>`到`<h6>`，定义不同级别的标题。
  ```html
  <h1>This is a Level 1 Heading</h1>
  <h2>This is a Level 2 Heading</h2>
  ```
- **段落标签**：`<p>`，定义段落。
  ```html
  <p>This is a paragraph of text.</p>
  ```
- **文本修饰**：
  - **`<strong>`**：加粗文本。
  - **`<em>`**：斜体文本。
  - **`<u>`**：下划线文本。
  - **`<s>`**：删除线文本。
  ```html
  <p>This is <strong>important</strong> text.</p>
  <p>This is <em>emphasized</em> text.</p>
  ```

##### **1.3.2 列表标签**
- **无序列表**：`<ul>`，列表项用`<li>`表示。
  ```html
  <ul>
      <li>Item 1</li>
      <li>Item 2</li>
      <li>Item 3</li>
  </ul>
  ```
- **有序列表**：`<ol>`，列表项用`<li>`表示。
  ```html
  <ol>
      <li>First Item</li>
      <li>Second Item</li>
      <li>Third Item</li>
  </ol>
  ```

##### **1.3.3 图像与链接**
- **图像**：`<img>`，通过`src`属性指定图像路径，`alt`属性提供图像的描述。
  ```html
  <img src="image.jpg" alt="Description of Image">
  ```
- **超链接**：`<a>`，通过`href`属性指定链接目标。
  ```html
  <a href="https://example.com">Visit Example</a>
  ```

##### **1.3.4 表格**
- **表格**：`<table>`，通过`<tr>`定义行，`<td>`定义单元格。
  ```html
  <table border="1">
      <tr>
          <th>Header 1</th>
          <th>Header 2</th>
      </tr>
      <tr>
          <td>Row 1, Cell 1</td>
          <td>Row 1, Cell 2</td>
      </tr>
      <tr>
          <td>Row 2, Cell 1</td>
          <td>Row 2, Cell 2</td>
      </tr>
  </table>
  ```

##### **1.3.5 表单**
- **表单**：`<form>`，用于收集用户输入。
  ```html
  <form action="/submit" method="post">
      <label for="username">Username:</label>
      <input type="text" id="username" name="username"><br><br>
      <label for="password">Password:</label>
      <input type="password" id="password" name="password"><br><br>
      <input type="submit" value="Submit">
  </form>
  ```

##### **1.3.6 容器标签**
- **`<div>`**：块级容器，用于布局。
- **`<span>`**：行内容器，用于文本包装。
  ```html
  <div class="container">
      <h1>Welcome</h1>
      <p>This is a paragraph inside a div.</p>
  </div>
  ```

#### **1.4 HTML属性**
HTML标签可以有属性，用于提供附加信息。属性以“名称-值”对的形式出现，放在开始标签中。常见的属性包括：
- **`class`**：为元素定义类名，方便CSS样式化。
- **`id`**：为元素定义唯一标识符，常用于JavaScript操作。
- **`style`**：直接在标签中定义样式。
- **`src`**：用于链接外部资源（如图片、脚本）。
- **`href`**：用于指定超链接的目标URL。

#### **1.5 HTML语义化**
HTML5引入了许多语义化标签，用于更清晰地定义页面结构，提高可读性和搜索引擎优化（SEO）。常见的语义化标签包括：
- **`<header>`**：定义页眉。
- **`<footer>`**：定义页脚。
- **`<nav>`**：定义导航链接。
- **`<article>`**：定义独立的文章内容。
- **`<section>`**：定义文档中的一个区域。
- **`<aside>`**：定义与页面内容相关的辅助信息。

