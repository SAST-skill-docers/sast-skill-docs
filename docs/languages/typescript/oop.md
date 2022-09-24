# TypeScript 的面向对象

## 类语法

一般而言，使用类语法足以应对常见的各种需求，这种写法在很大程度上与 C++ 是类似的。TypeScript 语言之中的类允许声明类成员和方法的访问权限。这里我们编写一个简易的复数类作为演示，内部已经包含了构造函数、定义成员方法和静态方法等：

```typescript
class Complex {
    // 这部分在 JavaScript 中是不存在的，JavaScript 只要任意访问 this.propertyName 即可
    private real: number;
    private imaginary: number;

    constructor(_real: number, _imaginary: number) {
        this.real = _real;
        this.imaginary = _imaginary; // Declare members & initialize
    }

    print() {
        console.log(`${this.real} ${this.imaginary >= 0 ? "+" : "-"} ${Math.abs(this.imaginary)} i`);
    }

    static printInfo() {
        console.log("This is a complex class.");
    }
}

let com = new Complex(1, 2);
com.print(); // "1 + 2 i"
Complex.printInfo(); // "This is a complex class."
```

有一些细节需要注意：

- 类方法内使用类成员的时候必须使用 `this` 调用；
- JavaScript 的所有类成员和类方法均是公有的，JavaScript 不具有访问权限修饰符，尽管你可以看到我们使用 TypeScript 时标注了 `private` 属性，但这只是在编译时检查你的代码有没有不适当的访问，编译后如果你用 JavaScript 调用，仍然可以直接访问这些属性。

类继承使用关键字 `extends`，需要注意的是子类的构造函数**必须**手动使用 `super` 调用父类构造函数（否则无法使用 `this` 变量），对于已经被重写的父类方法，也可以使用 `super` 关键字调用：

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

## 类与接口

在 TypeScript 中可以直接将类名作为类型标注：

```typescript
class Complex {
    real: number;
    imaginary: number

    constructor(_real: number, _imaginary: number) {
        this.real = _real;
        this.imaginary = _imaginary; // Declare members & initialize
    }
}

let com: Complex = new Complex(1, 2);
```

更多的时候，我们只在意一个对象的部分属性，而不在意其他部分。抽象类依然是存在的，但在实际工程中更常用的是**接口**。

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

    事实上，`Base` 类的作用就是**约束子类必须具有 `val` 成员**和**约束子类必须重写 `print` 虚方法**，前者规定了子类必须具有的成员，后者规定了子类需要设计的接口格式（接口参数列表和返回值类型）。
    也就是说一个抽象父类的作用是约束子类应当具有的成员和对外开放的接口格式。
    在这个意义上，抽象类和接口是一致的。

```typescript
interface Complex {
    real: number;
    imag: number;
}
```

这里声明了一个名为 `Complex` 的接口，它要求对象包含类型为 `number` 的属性 `real` 和 `imag`，因此如果我们写这样的函数：

```typescript
function printComplex(com: Complex) {
    console.log(
        `${com.real} ${com.imag >= 0 ? "+" : "-"} ${Math.abs(this.imag)} i`
    );
}
```

就可以接受任意包含 `real` 和 `imag` 属性的参数，而不必要像类那样通过构造函数来构造。

类就必须具有接口的成员并实现接口的方法：

```typescript
interface Point { x: number; y: number; print: () => void; }

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
