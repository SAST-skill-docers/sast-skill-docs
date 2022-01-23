# 设计模式与行为型模式

设计模式是在长时间实践中，开发人员总结出的**优秀架构与解决方案**。学习设计模式将有助于经验不足的开发人员在实际开发中，灵活地运用面向对象特性，并能够快速构建不同场景下的程序框架，写出优质代码。

## 设计模式分类

- 行为型模式：关注对象行为功能上的抽象，从而提升对象在行为功能上的可拓展性，能以最少的代码变动完成功能的增减。
- 结构型模式：关注对象之间结构关系上的抽象，从而提升对象结构的可维护性、代码的健壮性，能在结构层面上尽可能的解耦合。
- 创建型模式

本章内容主要探讨设计模式中的行为型模式。

## 行为型模式分类

本章介绍三种行为型模式。

- 模版方法模式
- 策略模式
- 迭代器模式

### 模版方法 Template Method

模版方法是一种针对接口编程的设计。

模版方法的思想是，基类是一个模板（也可以称作原型）。基类体现的是“抽象概念”，里面定义若干个纯虚函数，这些函数提供了这个类的“接口”。

比如，如果一个操作有 `operation1`、`operation2` 两个步骤，我们可以定义一个基类：

```c++
class AbstractClass {
public:
    virtual void Operation1() = 0;
    virtual void Operation2() = 0;
};
```

这个操作可能有很多个版本。每个版本的实现细节不同。比如，`AbstractClass` 可能是“监视计算机节点的负载状态”的过程，`Operation1` 是“得到总内存”，`Operation2` 是“得到已占用内存”。而这两种操作在不同的环境下，比如 Win32 和 Win64 下可能是不同的。这些不同的细节则由子类负责实现。

在使用时，抽象类的算法骨架提供了大致方法，再由这个方法来根据需要调用具体类的实现细节。比如：

```c++
class ConcreteClassA: public AbstractClass {
    void Operation1() {
        cout << "Do Operation 1 of A" << endl;
    }
    void Operation2() {
        cout << "Do Operation 2 of A" << endl;
    }
};
```

这样当我们要拓展一种新的实现类时，重新对基类进行继承与实现即可，无需对已有的**实现类**进行修改。

#### 由模板实现多态

现在我们有了很多继承自同一个抽象类的实现类，该怎么实现多态呢？

我们知道，模板多态是依赖于**指针和引用**的。编译器能够通过指针和引用判断实际指向的类型，并且调用实际类型里面 override 了的虚函数。所以在使用模板模式的时候，常常创建基类指针来调用实现类的函数。

例：

```c++
#include <iostream>
using namespace std;

class AbstractClass {
public:
    virtual void Operation1() = 0;
    virtual void Operation2() = 0;
};

class ConcreteClassA: public AbstractClass {
public:
    void Operation1() {
        cout << "Do Operation 1 of A" << endl;
    }
    void Operation2() {
        cout << "Do Operation 2 of A" << endl;
    }
};

class ConcreteClassB: public AbstractClass {
public:
    void Operation1() {
        cout << "Do Operation 1 of B" << endl;
    }
    void Operation2() {
        cout << "Do Operation 2 of B" << endl;
    }
};

int main() {
    AbstractClass *abstract;
    abstract = new ConcreteClassB();
    abstract->Operation1();
    abstract->Operation2();
    return 0;
}
```

### 策略模式 Strategy Method

模板模式中，每个实现类里面可能有多个功能（比如 `Operation1`、`Operation2` 等）。可能有两个类，它们各有 $N$ 个功能，其中只有一个的实现有区别。在模板模式下，我们却必须实现两个类。这就很麻烦。

为了避免这种情况，使用策略模式。

策略模式：定义一系列算法并加以封装，使得这些算法可以互相替换。这样，一种算法就不需要依附于某个实现类了，而是自成一类。

**具体而言**：

- 每一种行为各自有方法虚基类 `A`、`B`

- 每一个方法基类 `A` 有若干具体的方法 `A1`、`A2`、`A3`​，每一种都会继承 `A`​

- 所有对象具有一个对象基类 `O`​，对象基类 `O`​ 含有所有的方法基类指针 `A*`​、`B*`​，从而实现多态。注意到对象基类如果仅含有方法基类指针，实际上没法调用方法基类的方法，故而还需要调用接口

- 每种对象是一个具体的对象类 `O1`、`O2`、`O3`​，每种都会继承对象类 `O`​，同时让方法基类指针 `A*`​、`B*` ​具体指向方法派生类上 `Ax`、`By`​

!!! note "例子"

    比如下面这个例子：有三种鸭子：MallarDuck（绿头鸭）、RubberDuck（橡皮鸭）、DecoyDuck（诱饵鸭子）

    - MallarDuck 会 quack 地叫，会飞
    - RubberDuck 会 queak 地叫，不会飞
    - DecoyDuck 不会叫，不会飞

    发现所有鸭子都有飞、叫两种功能，但是各自的实现不太一样。我们不在每种鸭子里分别实现，而是在基类鸭子里储存这两种功能的基类指针，然后让鸭子的实现类指向对应的两种功能的实现类。

    实现如下：

    ```c++
    #include <iostream>

    class FlyBehavior {
    public:
        virtual ~FlyBehavior() { };
        virtual void fly() = 0;
    };

    class QuackBehavior {
    public:
        virtual ~QuackBehavior() { };
        virtual void quack() = 0;
    };

    class Duck{
    public:
        Duck(FlyBehavior* p_FlyBehavior, QuackBehavior* p_QuackBehavior) {
            pFlyBehavior = p_FlyBehavior;
            pQuackBehavior = p_QuackBehavior;
        }

        virtual ~Duck() { };
        virtual void display() { };

        void performFly() {
            pFlyBehavior->fly();
        }

        void performQuack() {
            pQuackBehavior->quack();
        }

    private:
        FlyBehavior* pFlyBehavior;
        QuackBehavior* pQuackBehavior;
    };

    class FlyWithWings: public FlyBehavior {
    public:
        void fly() {
            std::cout << "I'm flying!!" << std::endl;
        }
    };

    class FlyNoWay: public FlyBehavior {
    public:
        void fly() {
            std::cout << "I can't fly" << std::endl;
        }
    };

    class Quack: public QuackBehavior {
    public:
        void quack() {
            std::cout << "Quack" << std::endl;
        }
    };

    class MuteQuack: public QuackBehavior {
    public:
        void quack() {
            std::cout << "<< Slience >>" << std::endl;
        }
    };

    class Squeak: public QuackBehavior {
    public:
        void quack() {
            std::cout << "Squeak" << std::endl;
        }
    };

    class MallardDuck: public Duck { // 继承了对象基类，就已经有了方法基类指针
    public:
        MallardDuck(
            FlyBehavior* fly_behavior = new FlyWithWings(), // 构造函数含有缺省值
            QuackBehavior* quack_behavior = new Quack())
                : Duck(fly_behavior, quack_behavior) { } // 绿头鸭的构造函数，飞行和叫声的基类指针分别指向了对应的实现类

        void display() {
            std::cout << "I'm a real Mallard duck" << std::endl;
        }
    };

    class RubberDuck: public Duck {
    public:
        RubberDuck(
            FlyBehavior* fly_behavior = new FlyNoWay(),
            QuackBehavior* quack_behavior = new Squeak())
                : Duck(fly_behavior, quack_behavior) { }

        void display() {
            std::cout << "I'm a Rubber duck" << std::endl;
        }
    };

    class DecoyDuck: public Duck {
    public:
        DecoyDuck(
            FlyBehavior* fly_behavior = new FlyNoWay(),
            QuackBehavior* quack_behavior = new MuteQuack())
                : Duck(fly_behavior, quack_behavior) { }

        void display() {
            std::cout << "I'm just a Decoy duck" << std::endl;
        }
    };

    int main() {
        Duck* mallard = new MallardDuck();
        mallard->display();
        mallard->performFly();
        mallard->performQuack();

        Duck*rubber = new RubberDuck();
        rubber->display();
        rubber->performQuack();
        return 0;
    }

    /*
    output:
    I'm a real Mallard duck
    I'm flying!!
    Quack
    I'm a Rubber duck
    Squeak
    */
    ```

    从上面的例子可以看出，策略模式不过是更加多态化的模板模式罢了。在模板模式下，每个类（比如在上面这个例子里是每种鸭子）中的 `Fly`、`Quack` 函数都在这个鸭子类里实现。而这里则是先定义了 `Fly`、`Quack` 这两种动作的基类，即 `QuackBehavior`、`FlyBehavior`。并且在在基类鸭子里储存了这两种基类类型的指针。

    然后，再通过继承关系实现了各种不同的 `Fly`、`Quack` 的实现类，并且在鸭子的实现类里让 `QuackBehavior`、`FlyBehavior` 的基类指针指向不同的 `Fly`、`Quack` 的实现类。

