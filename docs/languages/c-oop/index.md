# C++ 语言面向对象

![](https://p9.itc.cn/q_70/images03/20211214/365060e046e6489887d140ebb2814c3d.jpeg)

## 前置知识

- 具有 C/C++ 语言基础，能够编写基本的 C/C++ 代码

- 有基本的抽象思维能力

## 前言

这位读者，非常欢迎你点开这份技能文档。如你所见，这份文档与技能文档项目中的其他文档不尽相同。没错，技能文档的初衷主要是希望能够超脱计算机系大多 fly bitch 的课程本身，默默地带给文档的阅读者课堂以外的能力提升。

而你即将阅读的 OOP 文档却不尽相同，这份文档是主笔人 zhaochen20 在 2021 年春季学期的课程笔记。好事多磨，这份文档几经周折，直到 2022 年才得以与智能体的活动推广一同上线。然而为时不晚，一字班的同学开春之后才会学习 OOP 课程，希望这份文档能够加深阅读者对 C++ 语言乃至计算机系统的一些认识。同时，也希望这份文档作为主笔人与审核人给予 2021 年的一句道别。

将原本的 Markdown [链接附在此处](https://cloud.tsinghua.edu.cn/d/efad6ff51b394ab49550/)，里面还有一些期末备考的资料，建议妥善利用。

## 学习路径

建议结合 [刘知远老师的课件](https://cloud.tsinghua.edu.cn/d/0cf641207e874e0a8b69/) 一同学习，虽然这份文档完全展开了课堂内容（因为 OOP 课程多少还是比较言简意赅的），然而看着课件能够对学习脉络更为清晰。

另外，在学习 OOP 初期，会讲述配置环境的问题。不得不说，在学习 OOP 的过程过程中，没有理解到 Makefile 的精华是一大遗憾。然而我个人认为直接上来就学习 Makefile 还是比较 fly bitch。

给出的建议是，如果你使用 Windows 电脑，先给自己的电脑配好 WSL2，然后学习基本的 Linux 命令（可以参考 [Linux 文档](/basics/linux)）。另外，这里可以推荐 [《第一行代码 Linux 命令行》一书](http://product.dangdang.com/29341422.html)，的确对命令行讲的很清楚。如果你是 MacOS 用户（不管是黑苹果还是白苹果），请务必给自己的电脑装上 `iterm2`，同时自行谷歌如何优化 `iterm2`。

此处再附上 zhaochen20 个人博客里的 [UNIX 学习笔记](https://zhaochenyang20.github.io/2022/01/12/Linux/%E6%AF%8F%E5%A4%A9%E4%B8%80%E4%B8%AAUnix%E5%85%A5%E5%9D%9F%E5%B0%8F%E6%8A%80%E5%B7%A7/)。顺带强推一波 MacOS 电脑，如果你不打游戏的话，我想没有比 MacOS 更适合计算机系的电脑了。

最后，由于笔记实际上绝大多数都是主笔人 zhaochen20 一人所写，难免有所错误。

如果有误，可以前往 zhaochen20 的 [博客留言](https://zhaochenyang20.github.io/)。

## 致谢

首先，本文档的内容完全基于刘知远老师的课程体系而建立，诚挚地感谢刘知远老师与他的教学团队对 OOP 课程的悉心付出。

随后，致谢主笔人 zhaochen20 本人，他在 2021 年 5 月份狂肝了大概 112 小时写出了初稿。

而后，对整理且做修改工作的 yiyj13 致以感谢，祝愿他进入信息学院后能够有所收获有所成。

最后，正如细心的读者可能发现的那样，这份文档最后两节的文风与前文不尽相同，自然，这并非主笔人完成。

出于一些特定的缘由，也表示对最后两节作者的尊重，主笔人在此真诚地感谢他的一位故人。

<s> Without whom, this tutorial would have been released half a year earlier.  </s>

<s> Not every story has an happy endding. Anyway, this is why we shall call it a story. </s>

期望以后的主笔人能够苦中作乐，整理文稿的同学亦能心想事成，也祝愿阅读文档并学习 OOP 课程的同学能够有所收获，在 2022 年智能体比赛中斩获佳绩。

---

本 OOP 文档最终由 Ashitemaru 格式化后代为 Commit 合并进入技能引导文档。

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