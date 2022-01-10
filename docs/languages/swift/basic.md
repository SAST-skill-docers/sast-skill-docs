为了更好地理解正文，我们在这里列举一些与正文有关的基础概念/知识：

### 概念

- Swift 是强类型静态语言，任何变量都有在编译期确定的、不可更改的类型
- Swift 类型系统在明确的上下文中可以自动推断类型
- Swift 不建议手动管理内存，没有指针
- Swift 没有头文件，定义和实现在同一个地方

### 基本语法

- Swift 使用换行分隔不同的语句，使用 `;` 分隔处在同一行的不同语句（不建议使用）
- Swift 中使用 `//` 或 `/* */` 进行注释
- Swift 中使用 `///` 进行文档的注释

### 代码规范

- 使用驼峰式命名，变量和函数小写开头，如 `myVariable`，类型名称大写开头，如 `MyStructure`
- 尽量使用单词的全称而非缩写命名，无论名称有多长，如 UIKit 中的 [`UIAdaptivePresentationControllerDelegate`](https://developer.apple.com/documentation/uikit/uiadaptivepresentationcontrollerdelegate)