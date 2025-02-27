# Rust语言基础

## 前置知识

- 具有 C/C++ 语言基础，能够编写基本的 C/C++ 代码

- <del>具有面向对象程序设计的基础</del> （如果你已经熟悉 C++ 的面向对象模型，那么你可能需要花一点时间来适应 Rust）

## 后续拓展

直到现在，Rust 仍然是一门主要由爱好者使用而非工业使用的语言。尽管如此，Rust 已经在许多领域崭露头角：Firefox 浏览器、Linux 系统等大型工程中都有Rust 的参与。

在操作系统这门课程中，同学们可以采用 [Rust框架](https://github.com/rcore-os/rCore) 来进行实验。

## 注意

不同于 JavaScript 和 Python 等语言，Rust 语言上手相对较为困难。因此，编写本文档的目的更类似于在假定读者拥有一定 C++ 基础的情况下，向读者提供一些学习 Rust 的对照性帮助，而非一个 Rust 入门教程。如果希望获得一份 Rust 入门教程，请参阅下方资源链接中的 The Book。

本文档覆盖了 The Book 的 第3（常见编程概念）、5（结构体）、6（枚举和模式匹配）章的主要内容，介绍了最为基本的语法。在文档的开头，将简要介绍 Rust 中的所有权。生命周期、不安全代码、自动测试等高级概念，可以参考下述资源链接自行学习。

## 资源链接

### 官方参考资料
- The Rust Programming Language（也被称为 The Book）。Rust 官方网站推荐的 Rust 语言入门书籍 https://doc.rust-lang.org/book/
    - The Book 的中文社区翻译版，更新很及时 https://kaisery.github.io/trpl-zh-cn/

- Rustlings。Rust 官方推荐的另一种入门方式，适合喜欢边学边做的同学 https://github.com/rust-lang/rustlings/

### 入门教程
- Rust 官方文档中文教程。https://rustwiki.org

- RUNOOB.COM 的 Rust 教程：简明的入门教程，可作为第一份学习资料。https://www.runoob.com/rust/rust-tutorial.html

### 阅读材料
- Effective Rust。Rust 进阶教程，比较详细地介绍了 Rust 中类型（Types）、特型（Trait）及 Rust 语言的设计理念和使用方法。 https://www.lurklurk.org/effective-rust/

### 课程材料
- CIS 198: Rust Programming：宾夕法尼亚大学的 Rust 课程。https://cis198-2016s.github.io

- 清华大学程序设计训练（Rust）文档：https://lab.cs.tsinghua.edu.cn/rust/