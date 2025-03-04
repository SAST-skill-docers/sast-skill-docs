

### **CSS（层叠样式表）**
CSS 是一种用于控制网页样式和布局的语言，它可以让开发者定义网页的外观，例如字体、颜色、间距、布局等。CSS 的主要作用是将HTML文档的结构与样式分离，从而提高开发效率和页面的可维护性。

#### **1. CSS 的作用**
- **样式设计**：定义字体、颜色、背景、边框等视觉元素。
- **布局控制**：通过CSS的布局模型（如Flexbox、Grid、定位等）来安排页面元素的位置。
- **响应式设计**：通过媒体查询（Media Queries）实现不同设备上的自适应布局。
- **动画和交互**：创建简单的动画效果（如过渡、关键帧动画）和交互反馈（如悬停效果）。

#### **2. CSS 的基本语法**
CSS 的基本语法由选择器和声明块组成：
```css
选择器 {
    属性1: 值1;
    属性2: 值2;
    ...
}
```
- **选择器**：用于选择页面中的HTML元素，例如类选择器（`.class`）、ID选择器（`#id`）、标签选择器（`div`）等。
- **声明块**：包含一系列属性和值，用于定义样式。

#### **3. 示例代码**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS 示例</title>
    <style>
        /* 样式定义 */
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            color: #333;
        }
        .box {
            width: 200px;
            height: 200px;
            background-color: #4CAF50;
            color: white;
            text-align: center;
            line-height: 200px;
            border-radius: 10px;
            transition: transform 0.3s ease;
        }
        .box:hover {
            transform: scale(1.1);
        }
    </style>
</head>
<body>
    <div class="box">Hover over me</div>
</body>
</html>
```
在这个示例中，CSS 定义了一个绿色的盒子，并在鼠标悬停时放大。

