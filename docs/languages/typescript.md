# TypeScript 语言基础

## 前置知识

- 具有 JavaScript 语言基础，能够编写简单的 JavaScript 语言程序

- 具有面向对象程序设计的基础，能够理解类封装和方法重写（语言最好是 C++）

## 简单的类型标注

TypeScript 最大的特点就是引入了类型系统，这样就可以在编译为 JavaScript 代码之前由编译器进行类型检查。在这样的条件下，TypeScript 中的变量在声明的时候就可以指定类型，编译器在将 TypeScript 代码编译为 JavaScript 代码的时候会进行类型检查，若有不符合类型声明的情况则会报错：

```typescript
const foo = (name: string): void => {
    console.log("Hello, " + name);
}

foo(2); // Error!
```

由于 TypeScript 是 JavaScript 的超集，所以其基础变量类型依然和 JavaScript 一致。常用的基础类型包括：

```typescript
let isDone: boolean = false;
let clickCnt: number = 6;
let greeting: string = "Hello!";
```

数组类型按照下面的代码声明：

```typescript
let list: number[] = [1, 2, 3, 4, 5];
```

对于无法明确确定类型的变量，可以声明为 `any` 类型：

```typescript
let notSure: any = 1;
notSure = "Maybe it is a string"; // OK
```

!!! caution "注意可能的 `any` 类型滥用"

    `any` 类型是目前 TypeScript 语言之中具有较大争议的一个设计，因为理论上我们可以将所有的变量声明为 `any` 从而绕过类型检查，这个时候 TypeScript 实际上退化为 JavaScript 。

    但是考虑到目前 Web 前端项目会引用大量的第三方库，开发者很多时候无法完全把握某些变量的信息，所以 `any` 类型是必要的。不过我们需要注意其使用，对于能够给定类型的变量则尽量不标记为 `any`。

    针对这一点，部分代码检查工具已经实装了检查“可疑 `any`”的功能。

更为复杂的类型可以包括：

```typescript
// Detailed object
let obj: {
    name: string,
    age: number,
    address: string
} = {
    name: "qiubao",
    age: 57,
    address: "Tsinghua"
};

// Detailed array
let arr: [number, boolean] = [1, false];
```

函数的参数和返回值的类型标注如下：

```typescript
function sum(x: number, y: number): number {
    return x + y;
}
```

另外，如果你认为你对某个变量的类型已经做了充分的检查，认为不需要编译器检查其类型，则可以进行**类型断言**：

```typescript
let val: any = "A string";
let stringLen: number = (val as string).length;
```

第二句的含义就是断言 `val` 变量一定是字符串。

## 联合类型和类型收窄

TyepScript 支持将变量声明为若干个类型之中的一种，这一般称为联合类型（英语：Union type）。声明联合类型的时候，多个类型之间使用 `|` 隔开：

```typescript
let union: number | string = 7;
union = "string"; // OK
```

联合类型最常用的地方是标注函数参数，这样就允许了函数接受多种类型的参数：

```typescript
const addPrefix = (x: number | string): void => {
    console.log("Prefix " + x);
};

addPrefix(1); // OK
addPrefix("String"); // OK
```

但是要注意，在这种函数之中，我们直接调用被声明为联合类型的变量的属性的时候，**只能调用这些类型所共有的属性**。比如说：

```typescript
const foo = (x: number | string) => { let y: number = x.length; }; // Error!
```

原因是编译器并不能清楚此处的变量的具体类型。这个问题的解决方法是使用条件分支将这些类型分离开来：

```typescript
const print = (x: number | string): void => {
    if (typeof x === "string") {
        console.log("[string] " + x);
        return;
    }
    // TAG
};
```

编译器实际上就已经明白在 `if` 分支之后的 `// TAG` 部分 `x` 只有可能是 `number` 类型，从而就可以断定属性调用是否符合类型标注。这被称为类型收窄（英语：Type narrowing）。

!!! note "联合类型实现枚举类型"

    TypeScript 的类型系统实际上还支持如下这种类型标注：

    ```typescript
    let a: 1;
    a = 1; // OK
    a = 2; // Error!
    ```

    其允许我们将数字或字符串字面量作为类型的一种，这种性质和联合类型一并使用的效果就类似于枚举：

    ```typescript
    type TwoPowers = 1 | 2 | 4 | 8 | 16;
    let a: TwoPowers = 2; // OK
    let b: TwoPowers = 5; // Error!
    ```

!!! note "`never` 类型与耗尽检查"

    TypeScript 语言支持一种特殊的类型，即 `never` 类型。这种类型常被用于标注函数返回值，代表这个函数永远不会终结或者会抛出异常：

    ```typescript
    const neverEnd = (): never => {
        while (true) {}
    };
    ```

    这个性质就使得**任何变量均不可能是 `never` 类型**，利用这个特点，我们可以检测**程序是否考虑了所有的情况**，这被称为耗尽检查（英语：Exhaustive check）。

    例如我们需要编写函数处理联合类型 `number | string | boolean`，其代码可能为：

    ```typescript
    type All = number | string | boolean;
    const handle = (x: All): void => {
        switch (typeof x) {
            case "number":
                // DO SOMETHING
                break;
            case "string":
                // DO SOMETHING
                break;
            case "boolean":
                // DO SOMETHING
                break;
            default:
                let exhaustiveCheck: never = x;
                break;
        }
    };
    ```

    理论上 `default` 分支之中的代码只要执行就必然报错，所以说如果所有的 `case` 分支穷尽了可能的情况，就不会触发错误。而如果某日 `All` 类型添加了新的可能类型：

    ```typescript
    type All = number | string | boolean | Foo;
    ```

    那么 `handle` 函数就有可能因为没有耗尽所有可能而报错，提醒开发者需要更新这个函数。这种处理方式类似于 C++ 程序在不可能的分支上加入 `assert(false)` 进行检测。

## 类与接口

TypeScript 语言之中的类类似于 ES6 标准中 JavaScript 语言之中的类，故此处仅给出样例程序而不进行详细讲解。不过需要注意的是，TypeScript 语言之中的类允许声明类成员和方法的访问权限。这里给出一个简单的复数类的代码，其中包含了类成员声明、类方法编写、构造函数等基础内容：

```typescript
class Complex {
    private real: number;
    private imaginary: number;
  
    constructor(_real: number, _imaginary: number) {
        this.real = _real;
        this.imaginary = _imaginary;
    }
  
    public print(): void {
        console.log(`${this.real} ${this.imaginary >= 0 ? "+" : "-"} ${Math.abs(this.imaginary)} i`);
    }
}

let z: Complex = new Complex(2, 3);
z.print();
```

类继承使用关键字 `extends` ，需要注意的是子类的构造函数**必须**手动使用 `super` 调用父类构造函数（否则无法使用 `this` 变量），对于已经被重写的父类方法，也可以使用 `super` 关键字调用：

```typescript
class Animal {
    private name: string;
  
    constructor(_name: string) { this.name = _name; }
    public move(): void { console.log("Animal moves"); }
}

class Cat extends Animal {
    private furColor: string;
  
    constructor(_name: string, _color: string) {
        super(_name); // Must call super() here!
        this.furColor = _color;
    }
  
    public move(): void { console.log("Cat runs"); }
  
    public baseMove(): void { super.move(); }
}

let tom: Cat = new Cat("tom", "blue");
tom.move();
tom.baseMove();
```

抽象类依然是存在的，但在实际工程中更常用的是**接口**。因为抽象类实际上规定了一个对象应当具有的成员和对外开放的接口格式。

!!! note "抽象类和接口"

    我们回想在 C++ 语言之中的抽象类：

    ```c++
    class Base {
    protected:
        int val;

    public:
        virtual void print() = 0;
    };

    class Derive: public Base {
    public:
        virtual void print() override {
            std::cout << this->val << std::endl;
        }
    };
    ```

    事实上，`Base` 类的作用就是**约束子类必须具有 `val` 成员**和**约束子类必须重写 `print` 虚方法**，前者规定了子类必须具有的成员，后者规定了子类需要设计的接口格式（接口参数列表和返回值类型）。也就是说一个抽象父类的作用是约束子类应当具有的成员和对外开放的接口格式。在这个意义上，抽象类和接口是一致的。

TypeScript 语言之中接口的定义为：

```typescript
interface Point { x: number; y: number; print: () => void; }
```

之后一个类可以去实现这个接口，这个时候这个类就必须具有接口的成员并实现接口的方法：

```typescript
class Point3D implements Point {
    x: number;  y: number;  z: number;
    ...
    print(): void {
        console.log(`(${this.x}, ${this.y}, ${this.z})`);
    }
    ...
}
```

接口支持**可选参数**和**只读参数**。可选参数代表该参数可具有可不具有：

```typescript
interface Point { x: number; y: number; z?: number; }
let twoDimensionPoint: Point = { x: 1, y: 2 }; // OK
```

只读参数代表该参数只可读取不可修改：

```typescript
interface User { name: string; readonly password: string; }
let holder: User = { name: "holder", password: "111111" };
holder.password = "222222"; // Error!
```

## 后续拓展

TypeScript 作为目前较为热门的 JavaScript 语言“补丁”，可以被广泛应用于各种 Web 前端框架或者移动端框架。目前类似 React、React Native、Vue 等常用前端框架均提供了 TypeScript 语言支持。

后续可以阅读的文档有：

- React 框架入门 <https://docs.net9.org/frontend/react-easy>
- 现代 React 框架 <https://docs.net9.org/frontend/react-more>

---

可以应用 TypeScript 语言相关知识的课程：

- 软件工程

## 资源链接

- TypeScript 语言官方文档 <https://www.tslang.cn/docs/home.html>