# C++ 语言面向对象

## 前置知识

- 具有 C/C++ 语言基础，能够编写基本的 C/C++ 代码

- 有基本的抽象思维能力

## 后续拓展

面向对象是一种相当常用的编程模式，现代许多编程语言都支持这一模式。在熟悉了 C++ 语言下的面向对象之后，我们推荐读者去尝试了解其他语言之下的面向对象相关语法，如 Python、Java、JavaScript 等等，这对后续的学习也有所裨益。

```c++
class Student {
private:
    int age;
    std::string name;

public:
    Student(int _age, std::string _name):
        age(_age), name(_name) { }

    void print() { }
};
```

```python
class Student:
    def __init__(self, _age, _name):
        self.age = _age
        self.name = _name

    def print(self):
        pass
```

```javascript
class Student {
    constructor (_name, _age) {
        this.name = _name;
        this.age = _age;
    }

    print() { }
}
```