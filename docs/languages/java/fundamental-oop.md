# Fundamental OOP

## 前置知识

- 基本的 Java 语法。
- （非必须）C++、Python 等语言的 OOP 语法。

## Basic Terms used in OOP

- *OOP* 面向对象编程。
- *object* 对象: 数据和行为的集合。
- *class* 类: 定义对象的模版。

### Object 对象

- *object*是数据和行为的集合。
- 数据一般称为 *member variable*, *field*, *state*, *data* 等。
- 行为一般称为 *method*, *function*, *interface*, *protocol* 等。
- 面向对象编程原语是向*对象发送消息*，而不是面向过程编程中的*指令跳转* 。
- *发送消息*就应当遵循一定的协议 *protocol* ，比如发送消息的类型和消息的内容，这表现为方法的名字和参数。

### Class 类

- *class* 是 *object* 的模版，是 *object* 的*类型* 。
- *class* 定义了 *object* 所拥有的数据和行为，而 *object* 包含了具体的数据值和对应行为的真正执行者 。
- *class* 本身可以拥有一些数据和行为。
- 面向对象编程的基本流程为:
  1. 设计类 `class Car { /* ... */ }`。
  2. 创建/实例化对象 `var myCar = new Car()`。
  3. 向对象发送消息 `myCar.move()`。

## OOP in Java

- *基本上* 万物皆对象。
  - 基本数据类型除外。
- Java 使用*引用*来控制对象。
  - Java 只有按值传递。
- Java 拥有自动内存管理机制。
  - 你只需要关系如何`new`，而无需担心对象是何时或如何被`delete` 。
  - Java 拥有多种优秀的垃圾收集器GC实现。

## 基础OOP

???+ 关于package

    - 例如: `package tourofjava.ch2.lab1;`
    - Java 使用*包  package* 组织代码。
    - 包名使用`.`建立层次结构，应当与文件目录结构相同。
    - 包虽然有嵌套结构，但是父目录和子目录之间没有如包含等的任何关系，仅仅是名字不同。
    - 包名通常全小写且单词之间无分隔。

### Declare a Class

```java
public class Car { ... }
```

- `public`是*访问控制关键字*的一种。
  - `public`代表其他所有 *class* 都可以访问，`private`代表只有这个 *class* 本身可以访问。
  - 应用于*class* *field* *method*的声明中。
  - Java要求名为`<name>.java`的源文件中必须包含同名的`public class`。

```java
public class Car { ... }
```

- *class* 通常采用大驼峰命名。

### Declare Member Fields

```java
public class Car {
    private String brand;
    private String model;
    private String color;
    private int enginePower;
    private int currentSpeed;
    private int maxSpeed;
    private int price;
    
    // ...
}
```

```java
private String brand;
```

- 访问控制 + 类型 + 成员域名 （通常采用小驼峰）。

- *class成员变量的*默认值。
  - 数字类型: `0` `0.0` 。
  - `boolean`: `false` 。
  - 对象: `null` 空指针 。
  
- 可以定义时赋初始值。

- `final` 修饰的成员变量是常量，因此除了声明赋值、构造函数、实例初始化块之外，不允许对其进行其他修改。

```java
private final String finalVar;
```

- `static`修饰的变量称为*class* 变量，它不属于对象而属于*class* ，因此访问的时候需要使用`<class_name>` 。
  
  ```java
  class Test { public static String var = "class var"; }
  
  System.out.println(Test.var);
  ```
- 通常使用`public static final`来声明一个全局类常量，常量的命名惯例是*全大写+用`_`分隔单词 * 。



### Constructor 构造器

```java
public class Car {
    public Car(String brand, String model, String color,
               int enginePower, int maxSpeed, int price) {
        this.brand = brand;
        this.model = model;
        this.color = color;
        this.enginePower = enginePower;
        this.maxSpeed = maxSpeed;
        this.price = price;
    }
}
```

```java
public Car(...)
```

- 访问控制 + *class* 名 + (参数列表) 。
- 用于初始化对象数据 。

```java
this.brand = brand;
```

- *class* 的成员方法可以访问类的域和方法而不需要`this`指定。
- 但是如果出现了变量或者形参名字冲突，则需要`this.`手动指定。

### Instance Initialization Block 实例初始化块

在类中直接使用`{}`扩起来的部分称为实例初始化块，它会在任何构造器运行之前运行。

```java
class Test {
    { System.out.println("This is an Instance Initialization Block"); }
    public Test() {
        System.out.println("This is a constructor");
    }
}

jshell> new Test()
This is an Instance Initialization Block
This is a constructor
```

### Static Initialization Block 静态初始化块

在类中使用`static {}`扩起来的部分称为静态初始化块。这个块会在类加载的时候运行一次。如果现在不是很清楚Java类加载的顺序和机制，那只需要记住，在绝大多数情况下，这个块在一个Java程序运行的过程中，会且只会运行一次。

### Member Methods 成员方法

```java
public class Car {
    public double move(double timeInSec) {
        double distance = currentSpeed / 3.6 * timeInSec;
        System.out.println("Move " + distance + " m in " + timeInSec + " s");
        return distance;
    }
    public void honk() {
        System.out.println("DiDi...");
    }
    public void introduce() {
        System.out.println("I'm a/an " + color + " " + brand + " " + model);
    }
}
```

- 成员方法的命名习惯一般为*小驼峰* 。
- 成员方法就是定义在类内部的方法/函数 。
- 并且也可以使用类内的成员变量和成员方法 。
- 类的`public`方法组成了类所定义的对象的公开接口/功能 。

### Getters & Setters

```java
public class Car {
    private String brand;

    public String getBrand() {
        return brand;
    }
    public void setBrand(String brand) {
        this.brand = brand;
    }
}
```

- 在大多数情况下，类的成员变量应当被封装为 `private` ，对他们的访问和修改应该由相应的方法处理 。
- 对于 `boolean` 型域，一般使用 `isXXX` `setXXX` 。
- 对于其他类型域，一般使用 `getXXX` `setXXX` 。
- 对于简单的读写操作，这些代码确实是没什么用的模版代码，但是 。
  - IDE 提供代码自动生成功能 。
  - Lombok 等库提供了编译时生成方法机制 。
  - Record 。


### Using Class & Object

```java
public class Car {
    public static void main(String[] args) {
        var myCar = new Car("BMW", "X7", "Black", 250, 245, 1_000_000);
        myCar.introduce();
        myCar.honk();
        myCar.setSpeed(100);
        myCar.move(600);
    }
}
```

```java
var myCar = new Car("BMW", "X7", "Black", 250, 245, 1_000_000);
```

- 使用`new`创建新的对象，这个对象的生命周期、内存位置将由 Java 自动管理 。
- `new` 会调用与提供参数类型对应的构造器。
  - 如果类不提供自定义构造器，那么默认提供无参数构造器。

```java
myCar.introduce();
```

- 使用`.`调用对象的方法/接口。

## Reuse Classes

所有人都喜欢代码重用。
在面向对象的语义中，类定义了几乎所有的行为和数据，那么代码重用指的*重用类*，即*用已有的类来生成新的类*的过程。

下面就来介绍面向对象中最基本的*重用类*方式:

- 组合 Composition。
- 继承 Inheritance。

### Composition 组合

- 组合指的是在新类中包含已有类的对象 *（的引用）*。
  - 你可能会说: 就这？那确实，就这。
  - 组合是最简单、但也是最基本的重用类的方式。
- 在我们的 `Car` 类中，我们实际上组合了若干个 `String` 对象来描述车的一些基本信息。

```java
public class Car {
    private String brand, model, color;
}
```

组合描述了 *"has-a"* 关系。

- 我们可以这种语义来建模更复杂、更贴合实际的汽车，如:
  - 一辆汽车 *有一个* 引擎。
  - 一辆汽车 *有四个* 轮子。
- 那么*汽车*的最大速度，就可以通过重用*引擎*中的数据或方法来进行计算。

组合对象的初始化:

- 前面提到，对象*引用*的默认值是  `null` ，对其进行任何访问都会引发空指针异常 `java.lang.NullPointerException`  。

```java
class Demo {
   private String s1 = "String 1", s2, s3, s4; // 1. definition
   public Demo() { s2 = "String 2"; }          // 2. constructor
   { s3 = "String 3"; }                        // 3. instance init
   public void useS4() {
      if (s4 == null) s4 = "String 4";         // 4. delayed init
   }
}
```

### Inheritance 继承

- *继承*是面向对象编程的重要特征之一。
- 在创建类的时候，总是使用了继承。在 Java 中，如果不手动指定，将会隐式继承自`java.lang.Object` 类，表达了任何对象的通用接口。
- *继承*将会使*子类*拥有所有*父类*的公开接口及其实现，并且可以访问*父类*中受保护的域和方法。
- 使用`extends`关键字声明继承。

```java
class Parent { ... }
class Child extends Parent { ... }
```

- Java不允许多重继承。原因在于:
  - 规避*diamond problem*，即继承具有相同接口的不同的类导致子类的接口实现的歧义。
  - 多重继承带来的设计负担与带来的架构优势不对等。

组合描述了 *"is-a"*  关系。让我们考虑任何OOP课程都会讲的几何图形的例子:

```java
public class Shape {
   public double area() { return 0; }
   public void draw() {}
   public boolean inside(double x, double y) { return false; }
}
public class Rectangle extends Shape { ... }
public class Circle extends Shape { ... }
public class Triangle extends Shape { ... }
```

在这个例子中:

- 矩形`Rectangle` 圆形`Circle` 三角形`Triangle` 都 *是一个* 几何图形 `Shape` 。
- `Rectangle` `Circle` `Triangle`都包含了`Shape`提供的三个公开接口，即获取表面积 `area()`  绘制 `draw()`  判断点是否在图形内部 `inside(double, double)` 。

**Inheritance:  Override/重载方法**

子类会继承父类的接口，但是大多数情况下，子类需要提供自己的实现。这时就需要 *override/重载* 方法。比如`Shape` 的 `area()`和 `inside(double, double)` 都是无意义的。

```java
public class Circle extends Shape {
   double x, y, r;
   public Circle(double x, double y, double r) { ... }
   public double area() { return Math.PI * r * r; }
   public boolean inside(double x1, double y1) {
      return (x1 - x) * (x1 - x) + (y1 - y) * (y1 - y) < r * r;
   }
}
```

子类的重载方法实现中，可能需要重用父类方法的实现。 Java 中使用`super`关键字表示使用父类的接口与数据。`super`名字取自*超类  superclass* 。

```java
public class Person { public String introduce() { ... } }

public class Student extends Person {
   public String introduce() {
      return super.introduce() + "24岁，事学生";
   }
}
```

重载父类方法需要函数名和参数类型与顺序都相同，如果误写那么这个方法就是子类全新的方法，这会导致可能错误的调用父类的方法。
如果明确知道自己想重载方法，可以使用`@Override`*注解*来让编译器“提醒”你。

```java
public class Circle extends Shape {
   @Override
   public double area() { return Math.PI * r * r; }
}
```

如果函数名不小心输入错误，那么编译器会很不高兴:

```java
public class Circle extends Shape {
   @Override
   public double arae() { return Math.PI * r * r; }
}
```

```
java: 方法不会覆盖或实现超类型的方法
```

为了避免这种 typo ，除了使用 `@Override` ，我还建议使用IDE来为你自动生成函数签名。如在 IntelliJ IDEA 中，使用 ` control/ctrl + O`  来自动重载方法。

*PS:毕竟 Java 语法还是比较繁琐的，我使用 IDE 就是为了延长我和我的键盘的寿命。*

父类中自然也会存在一些数据，所以可能有自定义的初始化/构造器实现。因为父类的成员域可能对子类不公开，即`private`，为了正确的初始化这些域，我们需要正确的使用父类的构造器。如果父类*有无参数构造器*，那么子类的所有构造器，如果没有显式指定，将会*首先调用*父类的无参构造器。

```java
public class Shape {
   // ...
}

public class Circle extends Shape {
   double x, y, r;
   public Circle(double x, double y, double r) { ... }
}
```

如果父类**没有无参数构造器**，那么子类必须使用 `super` 关键字调用父类的构造器。

```java
public class Person {
   private String name;
   public Person(String name) { this.name = name; }
}
public class Student extends Person {
   private String university;
   public Student(String name, String university) {
      super(name);
      this.university = university;
   }
}
```
子类调用父类的构造器的语句**必须**在子类构造器的第一行。
```java
public class Student extends Person {
   private String university;
   public Student(String name, String university) {
      this.university = university;
      super(name);
   }
}
```

```
java: 对super的调用必须是构造器中的第一个语句
```

这个限制是为了确保子类可以正确使用父类的域和方法。进而，`super()`的参数不可以传入、使用父类和子类的域或方法的返回值。

*PS: 不过确实有绕过这种限制的方法。但是试图访问未初始化的数据仍然是非常危险的，我在这里不介绍相关的方法，好奇的同学可以去查一查。*

下面的代码是非法的:
```java
class Parent {
   public String name;
   public Parent(String name) { this.name = name; }
}
class Child extends Parent {
   public Child() {
      super(super.name);
      // super(name);
   }
}
```

**Inheritance: Upcasting & Polymorphism 向上转型&多态**

继承带来的重要性质是*向上转型  Upcasting*和 *多态  Polymorphism*。

*Upcasting* 指的是子类对象可以转型为父类。
`Shape shape = new Circle(0, 0, 1);`

*Polymorphism* 指的是对父类函数的调用可能使用的子类的实现，会根据代码运行时，对象的实际类型进行动态分发。
`shape.area();  // Invoking Circle#area()`

**Inheritance: Polymorphism in Depth**

- 数据抽象、继承与多态时面向对象程序设计语言的三大基本特征.
- 多态的作用在于，消除实际类型之间的耦合关系，使得代码设计中尽可能的依赖于公开接口而非具体实现 。

**多态可以提升扩展性**。考虑下面更加实际的绘制图形的代码，图形的绘制函数接收一个画板对象，用于执行实际的绘图指令，如`drawLine(int, int, int, int)`等:

```java
class Shape { public void draw(Canvas c) {} }
class Circle extends Shape { @Override public void draw(Canvas c) { ... } }
class Rectangle extends Shape { @Override public void draw(Canvas c) { ... } }
class Triangle extends Shape { @Override public void draw(Canvas c) { ... } }
```

如果没有多态，我们需要根据具体的类型来调用具体的绘图实现:

```java
class GUI {
   private void render(Circle    shape) { shape.draw(getCanvas()); }
   private void render(Rectangle shape) { shape.draw(getCanvas()); }
   private void render(Triangle  shape) { shape.draw(getCanvas()); }
}
```

先不论这份代码做了多少重复工作，我们考虑加入一个新的图形子类`Ellipse`，那么`GUI`类还需要再添加一行`render`函数。

如果没有多态，我们需要根据具体的类型来调用具体的绘图实现:

```java
class GUI {
   private void render(Circle    shape) { shape.draw(getCanvas()); }
   private void render(Rectangle shape) { shape.draw(getCanvas()); }
   private void render(Triangle  shape) { shape.draw(getCanvas()); }
}
```

更有可能的是，`GUI` 是别人提供的代码实现，我们没有修改他们的权力。

如果使用多态，那么直接接收一个 `Shape`基类的对象，便可以实现运行时动态分发方法，并且也可以支持未来可能会添加的任意具体的图形子类。

```java
class GUI {
   private void render(Shape shape) { shape.draw(getCanvas()); }
}
```

***尽量不要在构造器中使用多态函数***，否则基类构造器在执行时，可能会错误的调用了子类的多态函数，但是此时子类的数据还未被正确初始化，这可能会导致错误的结果甚至异常崩溃。

因此，尽可能的用简单的语句进行成员变量初始化。

Java中不是所有事物都支持多态:

1. `static`和`final`方法是*前期调用绑定*，即他们所调用的方法在编译时已经确定。
2. 成员域不存在多态。如果存在重名，只会单纯的出现了标识符覆盖。对重名域的访问只和方法实现所在的类绑定。
3. `private`方法默认为`final`，因此即使重名也不会出现重载也不会支持多态。

**Inheritance: Downcasting**

可以向上转型就可以向下转型。前提是需要判断一下是否真的是某个具体子类然后再尝试转型，否则会触发`java.lang.ClassCastException`异常。

判断某个对象是否是一个类的实例，可以使用`instanceof`关键字:

```java
if (shape instanceof Circle) {
   Circle circle = (Circle) shape;
   // do something with circle
}
```

一个小语法糖: *[JEP394, Java16]* Pattern Matching for `instanceof`

```java
if (shape instanceof Circle circle) {
   // do something with circle
}
```

### Composition vs. Inheritance

- 两种方法都可以重用现有类的代码。

- 组合适用于我们需要*使用*现有类的功能与接口的情况，继承适用于我们想新建一个现有类的特例，我们可以重新实现和扩展现有类的接口。

- 继承可能带来的问题是*破坏封装*，即子类被允许暴露父类的受保护域和方法，这通常是不推荐的。

- **组合优先于继承**。

  

**delegate 代理**

考虑一辆可以载一些人的公共汽车，我们可以让公共汽车继承自`HashSet`来实现保存车上乘客信息的功能:

```java
class Person { ... }
class Bus extends HashSet<Person> { ... }

var bus = new Bus();
bus.add(new Person("c7w"));
bus.add(new Person("lambda"));
bus.add(new Person("xsun2001"));
```

显然，这个设计并不好。一方面，公共汽车应当*是一辆*载具，*有一些*乘客，我们应当*组合*一个乘客的集合。另一方面，直接继承会给公共汽车一堆本来不需要的数据和实现。

但是，我们确实需要提供类似*继承*的功能，因为我们确实需要加入 *add* *remove* *size* *clear* 这种和集合很类似的接口。

很多其他的语言提供了*代理  delegate* 或者*混入  mixin* 等语法特性和概念。但是 Java ~~显然~~没有相关的语法支持。

```java
class Bus {
   private Set<Person> passengers = new HashSet();
   public boolean add(Person p) { return passengers.add(p); }
   public boolean remove(Person p) { return passengers.remove(p); }
   public int size() { return passengers.size(); }
   public void clear() { return passengers.clear(); }
}
```

在 Java 中，可以通过组合+手动转发的方法实现 delegate。

### Abstract Class

某些类本身就是作为其他类的父类而存在。比如上面提到的 `Shape` 类，它提供的接口实际上都是没有意义的哑实现，必须要借助具体的子类才能起作用。在 Java 中可以使用*抽象类  Abstract Class *来表达。

```java
public abstract class Shape {
    public abstract double area();
    public abstract void draw();
    public abstract boolean inside(double x, double y);
}
```

抽象类可以包含*抽象方法  Abstract Methods *，也就是在签名中带有` abstract `关键字的方法。这些方法没有实现，类似于 C++ 中的*纯虚函数*。

抽象类不能被直接实例化，因为它们不提供具体接口的实现，还因为这在概念上是没有意义的:

```java
var shape = new Shape(); // Error
```

## Base of All: java.lang.Object

`java.lang.Object`是所有类的超类，它代表的语义也非常直观: 所有对象都*是一个*对象，确切的来说，是Java语言内的对象`java.lang.Object`。

根据继承的规则，任何类都包含了 `java.lang.Object` 中提供的公开接口，并应该在需要的时候提供正确的重载:

- `String toString()`
- `boolean equals(Object obj)`
- `int hashCode()`

其他函数与更加细致的描述参见API文档: [java.lang.Object ](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html) 。

对象的字符串描述: `String toString()`。

`System.out.println` 和字符串拼接都接受对象作为参数或操作数，它们利用的就是 `toString()` 方法将任何对象都转化为字符串。设计好的字符串描述对于日志、调试等方面都有重要作用。

默认的 `toString()` 实现返回的是`<类型>@<内存地址>`，如 `Circle@68be2bc2`，这显然没什么用。与其介绍怎么写比较好，我还是更推荐直接让IDEA自动生成一个就好了。

```java
@Override
public String toString() {
   return "Circle{" + "r=" + r + '}';
}
```

### Boolean Equals

`equals` 方法提供了比较对象*内容*是否相同、或者说在*逻辑上*而不是在*物理地址*上相同的实现。正确实现 `equals`  可以保证 Java 集合框架中判断包含、`Set` 去重复等基础语义的正确性。

1. *自反性*: `x.equals(x) == true`。
2. *对称性*: `x.equals(y) == y.equals(x)` 。
3. *传递性*: `x.equals(y) && y.equals(z)` $\Rightarrow$ `x.equals(z) == true` 。
4. *一致性*: 连续的调用`equals`方法的结果应当一致。
5. `x.equals(null) == false`。

但是，在 Java 中实现正确的`equals`实际上是一个较为困难的事情。我推荐的实现方法是:

```java
class Class {
   @Override
   public boolean equals(Object o) {
      if (this == o) return true; //如果两个对象引用相同，那内容一定相同
      if (!(o instanceof Class clazz)) return false; //如果不是这个类的对象，那么一定不相同
      return /* 依次比较各个域 */;
   }
}
```

- `if (!(o instanceof Class)) return false;` 如果不是这个类的对象，那么一定不相同。
  - 有时这个实现为: `if (o == null || getClass() != o.getClass()) return false;` 。
  - 两种方法的区别在于是否接受子类与父类相同。一般情况是接受的，例如不同实现的集合`Set`，如果包含元素相同，也应当认为是相同的。当然，具体的实现选用需要具体情况讨论。

- 比较各个域的方法。
  - 若为整数类型，直接`==`判断。
  - 若为浮点类型，使用`Double.compare`或`Float.compare`判断，这两个静态类函数可以帮忙处理`NaN`相关的情况。
  - 若为对象引用，使用`Objects.equals`判断，这个静态函数可以帮忙处理空指针。

*当然，如果没听懂，那就交给IDEA自动生成。如果你听懂了，也最好交给IDEA自动生成。*

选择: Template = `java.util.Objects.equals and hashCode (java 7+)`
并且勾选 "Accept subclasses are parameter to equals() method"。

### Hash Code

散列函数的有效实现可以提供`HashMap` `HashSet`等哈希表相关数据结构的正确性和高性能。

应当保证:

1. 相同的对象散列值必须相同: `x.equals(y) == true` $\Rightarrow$ `x.hashCode() == y.hashCode()`
2. 不同对象的散列值尽可能的不同
3. `equals()`和`hashCode()`**必须同时重载**。IDEA会帮你同时生成。

为了减轻程序员设计散列函数的负担，Java提供了`Objects#hash`静态方法计算散列值，你只需要:

```java
class Class {
   @Override
   public int hashCode() {
      return Objects.hash( /* 填入各个成员域 */ );
   }
}
```

## Interface

前面我们学习了重用现有类的技术。在这一大节中，我们将会认识扩展类的功能的技术: *接口 interface*

`interface` 实际上是` abstract class ` 的进一步抽象形式。` abstract class `允许含有不包含实现的抽象方法，而` interface ` 只定义了抽象方法，并且也不被允许有成员域（毕竟没有具体实现也不需要关联数据）。

`interface` 描述了对象之间可以使用的方法，定义了对象之间互相通讯的协议。因为其只含有方法声明，因此也没有了继承的*只允许单一继承*的限制。实现接口会让类的功能扩展，而不会引入其他的实现依赖。

### Using Interface

观察发现，我们的图形基类 `Shape` 只包含了抽象方法，因此可以直接转化为接口:

```java
interface Shape {
    double area();
    void draw();
    boolean inside(double x, double y);
}
```

- 使用 `interface ` 关键字声明接口。
- 接口中的方法应当都为 `public abstract` ，因此无需再次声明。

其他具体的图形类也都应该进行相应的修改:

```java
class Circle implements Shape {
    private double r;
    public Circle(double r) { this.r = r; }
    
    @Override public double area() { return Math.PI * r * r; }
    @Override public void draw() { /* ... */ }
    @Override public boolean inside(double x, double y) {
        return x * x + y * y < r * r;
    }
}
```

```java
class Circle implements Shape
```

使用`implements`关键字声明实现的接口列表。

### Static Fields

可以为`interface`内声明变量，但是这不是成员变量，而是默认的`public static final`变量，也就是接口的静态常量。

```java
interface Shape {
   int MAX_WIDTH = 100, MAX_HEIGHT = 100;
}

int width = Shape.MAX_WIDTH;
```

### Static Methods

`interface`内允许有`static`方法，同样默认为`public`可见性。

```java
interface Shape {
   static Shape createUnitCircle() { return new Circle(0, 0, 1); }
}

var unit = Shape.createUnitCircle();
```

### Default Methods

- `default` 提供默认的方法实现，这也意味着实现接口的类可以进行重载。
- 接口的某些方法可以由已有的方法导出，比如判断集合 `Set` 是否为空 `isEmpty()` 时，对于大多数集合来说，可以通过判断 `size() == 0` 实现。这时就可以使用 `default` 来避免每个子类都写一句 `size() == 0`。
- `default` 用于保证二进制兼容性。修改已有接口可能导致以前使用这个接口的类库出现方法未实现的错误。通过添加 `default` 函数可以避免这个问题。

但是这又引入了 *diamond problem* ，即类实现了两个具有相同 `default` 方法的接口时出现的实现歧义。

在 Java 中，如果出现这种情况，你*必须*重载这个冲突的方法来提供自己的实现。如果想使用提供的 `default` 实现，你应当通过 `<interface_name>.super.<func_name>()` 手动指定使用哪一个接口。

```java
interface Inter1 {
    default void foo() { System.out.println("Inter1"); }
}

interface Inter2 {
    default void foo() { System.out.println("Inter2"); }
}

class Test implements Inter1, Inter2 {
    @Override
    public void foo() { Inter1.super.foo(); }
}
```

- **`default` method VS `abstract` class**
- 可能有同学回想起了抽象类。确实，这两种机制都允许具体实现和纯接口混合，那么我们应当选择哪一种方案呢？
- **优先选择使用`interface`**
- 如果确实需要类提供的功能，如需要成员域、构造器、完全访问控制等只有类才具有的特性，才应该考虑抽象类。
- 接口没有单一继承限制，因此可以提升系统扩展性。


## Inner Class

除了命名上的嵌套关系，内部类最重要的一点就是，具有对*外围对象*的访问权，甚至可以访问 *外围对象* 的`private`成员。

```java
public class Outer {
    private String name = "Outer class";

    public class Inner {
        public String outerName () {
            return name;
        }
    }
}
```

如果出现命名覆盖或者想明确使用外围对象的成员，需要使用`.this`语法:

```java
public class Outer {
    private String name = "Outer class";

    public class Inner {
        private String name = "Inner class";
        public String outerName () {
            return Outer.this.name;
        }
    }
}
```

可以在外部类里直接实例化内部类。如果不在外部类内，那么必须显式提供外围对象的引用，并使用`.new`语法实例化:

```java
public class Outer {
    public class Inner { /* ... */ }
    public Inner innerInstance() { return new Inner(); }
}

// Outside of class Outer
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
```

内部类提供了隐藏实细节的一种方式。通过调整内部类的访问控制，外部系统甚至有可能找不到具体接口实现或子类的具体对象。这完全阻止了依赖于类型的编码，完全隐藏了实现细节。

内部类提供了另一种*多重继承*的实现方案。通过返回内部类的实例，可以向外部提供继承自不同父类的对象，并且这些对象都可以访问外围对象内的任何数据和方法。

比如我们想让一个类同时提供 `Class1` `Class2`  的功能，但是我们又不能同时继承自这两个类，那么我们可以为我们的类提供两个内部类 `SubClass1` `SubClass2` ，分别继承自两个父类，并在需要某个类的功能时，实例化并返回对应内部类的对象。

这相当于共享充电宝的三个不同插口的充电线，我们需要哪个规格的插口，就“调用对应的方法”获取充电线的实例。

```java
class AppleLightning { /* ... */ }
class UsbTypeC { /* ... */ }
class UsbMicroTypeB { /* ... */ }

public class PowerBank {
    private int powerLeft;

    class AppleLightningImpl extends AppleLightning { /* ... */ }
    class UsbTypeCImpl extends UsbTypeC { /* ... */ }
    class UsbMicroTypeBImpl extends UsbMicroTypeB { /* ... */ }

    public AppleLightning lightning() { return new AppleLightningImpl(); }
    public UsbTypeC       typeC()     { return new UsbTypeCImpl(); }
    public UsbMicroTypeB  microB()    { return new UsbMicroTypeBImpl(); }
}
```

### Local Inner Class 局部内部类

你可以在方法内部甚至任意的作用域内定义类。

```java
public class PowerBank {
    private int powerLeft;
    
    public AppleLightning lightning() { 
        class AppleLightningImpl extends AppleLightning { /* ... */ }

        return new AppleLightningImpl();
    }
}

```

### Anonymous Inner Class 匿名内部类

观察下面的例子:

```java
public class PowerBank {
    private int powerLeft;
    
    public AppleLightning lightning() { 
        return new AppleLightning() {
            // Implements of AppleLightningImpl
        };
    }
}
```

`new BaseClass() { /* ... */ };`创建了一个匿名内部类的对象，这实际上等价与（类名为编译器生成）:

```java
class BaseClass$1 extends BaseClass { /* ... */ }
new BaseClass$1();
```

匿名内部类不允许有构造器，因为显然我们并不知道匿名类的名字。如果我们需要对类进行初始化操作，需要使用*实例初始化*:

```java
public AppleLightning lightning() { 
    return new AppleLightning() {
        { System.out.println("Init AppleLightning"); }
    };
}
```

在匿名内部类中使用的外部变量必须为 `final` 或者*等效` final `的*。

```java
interface Calculator { int apply(int a); }

Calculator mul(int x) {
    // x = 10; x is effectively final
    return new Calculator() {
        @Override int apply(int a) { return x * a; }
    }
}

mul(5).apply(3); // == 15
```

Java 对匿名内部类所用外部变量的 `final` 要求源自于内部管理闭包的机制: Java会在创建匿名内部类时，自动将所需的外部变量拷贝进去。如果后面对外部变量进行修改，那么匿名内部类中的值和外部变量的值会不一致，出现令人疑惑的语义问题。因此Java设计者让这些外部变量为`final`，不允许修改。

### Lambda Expression

以上的例子中，语法繁琐（不仅要`new`还得重写一遍方法签名)。Java  有如下的语法特性（Lambda Expression）:

```java
Calculator mul(int x) {
    return (int y) -> x * y;
}
```

Lambda表达式的参数列表也支持`var`自动推断:

```java
Calculator mul(int x) {
    return (var y) -> x * y;
}
```

Lambda表达式实际上可以看做一种特殊的匿名内部类:

- 只允许实现*函数式接口*，也就是只含有一个方法的接口。这种接口只代表了这个方法，也称为函数。
- 比如我们的 `Calculator` 就只包含一个方法 `int apply(int)` ，因此可成为函数式接口。

关于Lambda表达式的其他用法和标准库支持，我们会在*高级语法特性*章节中详细讲解。

## Enum 枚举类

*枚举 Enum*是一种特殊的类，用于为一系列相关的常量提供命名空间和更好的语义。

```java
public enum Size { SMALL, MEDIUM, LARGE, EXTRA_LARGE }
```

这等价于:

```java
public class Size extends Enum<Size> {
    public static final Size SMALL       = new Size();
    public static final Size MEDIUM      = new Size();
    public static final Size LARGE       = new Size();
    public static final Size EXTRA_LARGE = new Size();
}
```

枚举类也可以有构造器，我们需要声明枚举项的同时提供构造器的参数:

```java
public enum Size {
    SMALL("S"), MEDIUM("M"), LARGE("L"), EXTRA_LARGE("XL");
    
    private String abbr;
    private Size(String abbr) { this.abbr = abbr; }
    public String getAbbr() { return abbr; }
}
```

可以像访问类常量一样访问枚举项: `var mySize = Size.LARGE;`

`toString()`被实现为返回枚举项的名字:

```java
jshell> Size.SMALL.toString()
$1 ==> "SMALL"
```

`ordinal()`返回枚举项的次序:

```java
jshell> Size.EXTRA_LARGE.ordinal()
$2 ==> 3
```

更多方法参见API文档: [java.lang.Enum<E>](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Enum.html)。

## Recap

在本章节*Fundamental OOP*中，我们学习了:

- 面向对象的基本概念。
- 创建、使用类的语法。
- 重用类的方法: 组合和继承。
- 扩展类的方法: 接口。
- 使用内部类进一步封装实现。
- 使用匿名内部类和Lambda表达式创建闭包。

## 资源链接

- [Object 类: java.lang.Object](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html)
- [Enum 类: java.lang.Enum<E>](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Enum.html)
