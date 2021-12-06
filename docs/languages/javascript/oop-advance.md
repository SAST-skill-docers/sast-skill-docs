## JavaScript 的面向对象进阶

!!! note "一些可能不必要的知识"

    本章节将主要讲解 JavaScript 面向对象编程的原型链逻辑和私有方法的模拟，但这些知识对于 JavaScript 的初学者并不一定必要。所以编者建议如果只是想学习 JavaScript 基础语法后直接转 TypeScript 以及上手前端框架，则不需要浪费时间学习这一部分。想要深入了解这一门语言的读者，编者建议一定要学习这一部分。

### 对象原型

我们来从 C++ 的面向对象语法入手，观察这样的一个 C++ 类：

```cpp
class People {
public:
    int age;
    std::string name;

    void greet() {
        std::cout << "Hello, my name is " << this->age << ".\n";
    }
};
```
