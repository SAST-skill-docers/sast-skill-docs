Swift 一个重要的概念是类型。类型是 Swift 中实现面向对象编程（object-oriented programming）、面向协议编程（protocol-oriented programming）以及泛型编程（generic programming）的基础。

在 Swift 中，所有变量的类型要么属于某一种一等（first-class）类型：

- `class`
- `struct`
- `enum`
- `protocol`（一定条件下）

要么属于：

- tuple
- 函数

我们分别介绍上面的几种类型，在其中会讨论它们之间的联系与区别。

## class

Swift 中的 `class` 与 C++ 类似。

```swift
class Counter {
    private(set) var count = 0
    func increment() {
        count += 1
    }
    func increment(by amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}
```

### 成员函数、成员变量及其访问控制

Swift 中成员函数和成员变量的概念与 C++ 基本一致，一个类对象拥有独立的成员变量，成员函数可以访问所有的成员变量。

`class` 的访问控制通过在成员声明前加上修饰符实现，包括：

- `public`：**默认**，外界可访问，可写
- `private`：外界不可访问
- `private(set)`（成员变量）：外界可访问，不可写

在类中通过 `self` 引用自身，如果不会产生歧义，可以省略 `self`。

在类中时常会用到一种特殊的变量：计算变量。计算变量相当于一个**没有参数的函数**，每次获取其值的时候都要通过调用这个函数来计算：

```swift
class Rectangle {
    var width: Double
    var height: Double

    var area: Double {
        return width * height
    }
    ...
}

let rectangle = Rectangle(...)
print(rectangle.area)
```

计算变量是只读的，而且不会占用储存空间。如果计算的时间复杂度超过 $O(1)$，那么应该使用普通的函数，而不是计算变量。

### 构造函数和析构函数

构造函数使用关键字 `init` 声明。由于 Swift 没有零值初始化，所有成员变量都要有初值，要么直接在变量后显式指出，要么在构造函数中设定。构造函数结束时，所有成员变量都要有初值，否则产生编译错误。

```swift
class A {
    var bar: Int
    var foo = "Hello"

    init(bar: Int) {
        self.bar = bar
    }
}
let a = A(bar: 1)
```

如果类的每个成员变量都有默认值，那么可以使用默认构造函数：

```swift
class B {
    var bar = 1
    var foo = "Hello"
}
let b = B()
```

析构函数使用关键字 `deinit` 声明，不过在一般的开发中很少用到：

```swift
deinit {
    ...
}
```

### 静态变量、静态方法

使用 `static` 定义静态变量和方法：


```swift
class A {
    static let shared = A()
    static func foo() {...}
}

let a = A.share
A.foo()
```

### 类是引用类型

Swift 中的类是引用类型，传递引用而非副本，相当于指针。比如：

```swift
class Counter {
    var count = 0
}

let a = Counter()
let b = a   // b is a reference of a

b.count += 1
print(a.count)  // 1
```

Swift 中对类的实例的管理使用类似于智能指针的方法，当引用计数为 0 时，实例被销毁。另外，可以在 `var` 前加上 `weak` 声明不参与计数的 weak 变量。

### 继承

Swift 支持类的继承，但**不支持多重继承**。与 C++ 不同，Swift 的继承没有 `private`、`protected` 和 `public` 之分，所有继承都相当于 C++ 中的 `public`。

另外，Swift 中所有的函数都相当于 C++ 中的虚函数，在派生类中使用 `override` 即可重写基类的方法；使用 `super` 调用基类的方法：

```swift
class Base {
    func foo() {
        print("Base")
    }
}

class Derived: Base {
    override func foo() {
        super.foo() // prints "Base"
        print("Derived")
    }
}

let derived = Derived()
derived.foo() // "Base Derived"
```

使用 `as` 向下进行类型转换，使用 `as?` 向上进行动态类型转换，使用 `as!` 进行强制类型转换：

```swift
var a: Base = Derived()
let b = a as? Derived // 类型为 Optional<Derived>
// 或 if let b = a as? Derived {...}

let c = a as! Derived // 类型为 Derived，若转换失败则导致崩溃
let d = c as Base     // 类型为 Base，向下转换必然成功
```

## struct

与 C++ 不同，Swift 中的 `struct` 和 `class` 有共同之处，但并不能等同。

`struct` 与 `class` 类似，可以定义成员变量/成员函数/静态成员/构造函数，访问控制与类相同。

它们之间的不同点有：

- `struct` 是**值类型**。也就是说，传递的是原有实例的**副本**，而非引用
- `struct` 没有继承
- `struct` 不能定义析构函数

!!!note "与 `class` 进行对照"
    使用上面的例子：
    
    ```swift
    struct Counter {
        var count = 0
    }

    let a = Counter()
    let b = a   // b is a copy of a

    b.count += 1    // b.count == 1
    print(a.count)  // 0
    ```


`struct` 由于没有继承，因此对成员的构造函数（memberwise initializer）能够自动生成，大多数情况下无需手动写构造函数：

```swift
struct A {
    var foo: Int
    var bar: String
}

let a = A(foo: 1, bar: "Hello")
```

另外，`struct` 的成员函数默认不可更改成员变量，如果需要更改，则需要在函数前加上 `mutating`：

```swift
struct Counter {
    var count = 0
    mutating func increase() {
        count += 1
    }
}
```

!!! note "关于 `struct` 和 `class` 的使用"
    看上去 `struct` 比 `class` 功能弱了不少，但 `struct` 通过遵循协议（protocol，在下面介绍）可以实现继承的效果，同时由于传递的不是引用，因此大大降低了意料之外的对某个实例更改的概率。而且，`struct` 采用 copy-on-write 的策略，只有在更改时才会真正进行复制，因此性能上并没有太多的损失。

    实际上，现代的 Swift 框架，如 SwiftUI、Combine，已经几乎全部使用 `struct` 进行面向对象/面向协议的编程。官方的建议是：除非只能用 `class`，比如要在多个地方引用同一个实例，否则**优先使用 `struct`**。

## enum

Swift 的 `enum` 提供了一种类型安全的枚举方法。

```swift
enum CompassPoint {
    case north, south, east, west
}

var directionToHead = CompassPoint.north
```

`enum` 可以使用 `switch` 语句来匹配：

```swift
directionToHead = .south
switch directionToHead {
case .north:
    print("Lots of planets have a north")
case .south:
    print("Watch out for penguins")
case .east:
    print("Where the sun rises")
case .west:
    print("Where the skies are blue")
}
```

或者用 `==` 判断：

```swift
if directionToHead == .north {
    print("我飘向北方")
}
```

!!!note "类型省略写法"
    上面 `directionToHead` 的类型已经明确，因此可以将 `CompassPoint.south` 缩写为 `.south`。这种写法对于 `class` 和 `struct` 的静态成员和方法同样适用。如：

    ```swift
    struct Color {
        static let yellow = Color(...)
    }

    let color: Color = .yellow
    ```

### 迭代

可以在 `enum` 后面加上 `CaseIterable` 使得可以按照声明顺序枚举所有的类型：

```swift
enum CompassPoint: CaseIterable {
    case north, south, east, west
}

for point in CompassPoint.allCases {
    ...
}
```

### 原始值

`enum` 可以有一个原始值（raw value）类型，可以是 `Int`，`String` 等等，可以用来初始化 `enum` 类型。如果不指定值，编译期会自动设置对应的值，如 `Int` 是从 0 开始赋值，而 `String` 对应各个 `case` 的名称。

```swift
enum Status: Int {
    case onVacation
    case atSchool = 10
}

print(Status.onVacation.rawValue)   // 0
let status = Status(rawValue: 5)    // 类型为 Optional<Status>
```

### 关联值

`enum` 的每个 case 可以储存一个或多个关联值（associated value）。需要注意，一旦储存了关联值，就不能够指定 raw value，也不能够通过 `==` 判断是否相等：

```swift
enum Food {
    case hamburger(calories: Double)
    case juice(amount: Double)
}


let breakfast = Food.hamburger(calories: 294.9)

switch breakfast {
case .hamburger(let calories):
    print("I ate a hamburger with \(calories) calories")
default:
    break
}

// or
if case .hamburger(let calories) = breakfast {
    print("I ate a hamburger with \(calories) calories")
}
```

!!!note "Optional"
    `Optional` 类型实际上就是一个带有 associated value 的 `enum`：

    ```swift
    enum Optional<Wrapped> {
        case .some(Wrapped)     // has value
        case .none              // nil
    }
    ```

### 函数、构造函数和变量

`enum` 是 Swift 的一等类型，它可以有成员函数、构造函数、计算变量、静态函数、静态变量，但**不能储存值**，即不能拥有成员变量。

能够给 `enum` 添加函数等给编程带来了极大的便利：

```swift
enum HTTPError: Int {
    case badRequest = 400
    case notFound = 404
    case forbidden = 403
    ...
    case unknown

    init(code: Int) {
        if let error = HTTPError(rawValue: code) {
            self = error
        } else {
            self = .unknown
        }
    }

    var description: String {
        switch self {
        case .badRequest: return "Bad Request"
        case .notFound: return "Not Found"
        ...
        }
    }
}

let error = HTTPError(code: 404)
print(error.description)            // "Not Found"
```

另外，一般使用 `enum` + 静态变量来限定命名空间：

```swift
enum Constant {
    static let accentColor = Color.blue
    static let backgroundColor = Color.white
}

let color = Constant.accentColor
```

## protocol

Swift 中的 `protocol`（协议）与 C++ 的抽象类概念对应，其作用是**定义一组需要实现的接口**。比如，Swift 中的 `Equatable`，其作用是一个判等器：

```swift
protocol Equatable {
    static func == (lhs: Self, rhs: Self) -> Bool
}
```

如果一个类型想要**遵循** `Equatable`，需要：

1. 在类型名称后面加上 `Equatable`
2. 实现 `==` 方法

比如：

```swift
struct Rectangle: Equatable {
    var width: Double
    var height: Double

    static func == (lhs: Rectangle, rhs: Rectangle) -> Bool {
        return lhs.width == rhs.width && lhs.height == rhs.height
    }
}

let r1 = Rectangle(width: 1, height: 3)
let r2 = Rectangle(width: 1, height: 2)
print(r1 == r2)     // false

let rectangles = [r1, r2]   // An array of rectangles
print(rectangles.contains(Rectangle(width: 1, height: 2)))  // true
```

在这里，`Array` 本身并不知道如何判断它其中是否有元素与提供的 `Rectangle` 相等，但由于 `Rectangle` 遵循了 `Equatable` 协议，使得 `Array` 可以调用 `Equatable` 规定的判等接口来实现判断。

!!!note
    值得注意的是，这种接口的规定是严格的。`Array` 的 `contains` 方法当且仅当其元素类型 `Element` 为 `Equatable` 时才存在：

    ```swift
    extension Array where Element: Equatable {
        func contains(_ element: Element) -> Bool {...}
    }
    ```

    有关泛型和 `extension`，我们在后面介绍。

### 接口的规定方法

`protocol` 可以规定几类接口：

- 成员函数
- 成员变量
- 静态函数
- 静态变量
- 构造函数

```swift
protocol SomeProtocol {
    var foo: Int { get set }
    var bar: Int { get }
    static var staticMember: Int { get }

    func someMethod(a: Int) -> Int
    static func staticMethod() -> Bool
    init(code: Int)
}
```

其中，规定变量的接口时，只能用 `var`，而且需要在类型名后加上 `{ get }` 或 `{ get set }` 以指定**需要满足的最小权限要求**。比如，某个类型遵循 `SomeProtocol` 时，它的 `foo` 变量需要可读写，而 `bar` 变量**至少**可以读，但**不能限制其是否可写**。

### 协议作为类型

当没有 associated type（稍后介绍）时，协议可以作为类型，并且其真正的类型可以改变，如 Swift 中内置的 `Encodable`：

```swift
var someEncodable: Encodable = 1
someEncodable = "Hello"
```

### 协议方法的默认实现

在规定了协议的接口后，可以在 `extension` 中对除了 `init` 以外的接口进行默认实现，但不能在 `extension` 中实现储存变量：

```swift
extension SomeProtocol {
    var bar: Int {
        return 1
    }
    func someMethod(a: Int) -> Int {
        return a
    }
}
```

这样，当某个类型遵循 `SomeProtocol` 时，可以不用实现这些已经进行默认实现的接口。

### 协议的组合与继承

一个类型可以遵循多个协议，需要实现所有协议规定的接口：

```swift
protocol A {
    var foo: Int { get }
}
protocol B {
    func bar()
}

struct: SomeStructure: A, B {
    var foo: Int
    func bar() {...}
}
```

一个协议可以继承多个协议，其作用为添加这些协议的规定的接口作为自身的限制条件，需要实现所有的接口：

```swift
protocol A {
    var foo: Int { get }
}
protocol B: A {
    func bar()
}

struct: SomeStructure: B {
    var foo: Int
    func bar() {...}
}
```

!!!note "Potocol-oriented Programming"
    通过遵循协议、对协议进行默认的实现、协议的继承，使得 `struct` 和 `enum` 这些没有继承功能的类型可以因此实现类似于继承的效果。

    现代 Swift 框架大量使用 `protocol` 进行 OOP 的一个变种——面向协议的编程，即 POP。其思想与 OOP 并无二致，但在某些问题的处理上比类继承要更加简洁，不易出错。

## extension

一等类型（`class`、`struct`、`enum` 和 `protocol`）可以通过 `extension` 添加方法（计算变量或函数），包括所有的内置类型：

```swift
extension Int {
    var string: String {
        return "\(self)"
    }
}

print(1.string)
```

使用 `extension` 可以便捷地以 OOP 的方式给内置类型添加方法。

另外，一般为了区分不同的 `protocol` 的接口，在遵循不同的协议时，往往将各个协议规定的接口实现在不同的 `extension` 中：

```swift
struct A {
    // Original members
}
extension A: ProtocolA {
    // Implementation of ProtocolA
}
extension A: ProtocolB {
    // Implementation of ProtocolB
}
```

## 元组

元组（tuple）用于传递复合值，用括号组织，可以带标签，用标签或者成员（而非下标）0、1、2……取出其中的值：

```swift
func getCurrentTime() -> (hour: Int, minute: Int, second: Int) {
    return (18, 20, 36)
}

let time = getCurrentTime()
print(time.hour)
//or
print(time.0)   // hour
```

元组的元素数量是固定的，不存在越界的情况。

## 函数

Swift 中，函数也是一种类型，可以储存在变量中，也可以作为参数/返回值参与另一个函数。函数类型的声明为：`(参数列表) -> 返回类型`：

```swift
var someHandler: (Int, Int) -> Void
var optionalHandler: ((Int, Int) -> Void)?  // Optional
```

使用大括号来生成匿名函数，在括号内使用 `参数列表 -> 返回类型 in` 给传入的各个参数命名：

```swift
let incrementer: (Int) -> Int = { result -> Int in
    print(result)
    return result + 1
}

let result = incrementer(1)  // prints "1", result == 2
```

上下文明确时，可以省略括号内的 `-> Int`：

```swift
let incrementer: (Int) -> Int = { result in
    return result + 1
}
```

另外，可以省略参数列表，用 `$0`、`$1`……等指代第一、第二……个元素：

```swift
let incrementer: (Int) -> Int = {
    return $0 + 1
}

[1, 2, 3].removeAll(where: { $0 < 2 })  // [2, 3]
```

!!!note
    函数对象的一个典型应用场景是作为参数传入函数中作为回调函数。如，进行网络请求，当请求完成时，调用传入的回调函数来进行结果的处理：

    ```swift
    let task = session.dataTask(with: url) { data, response, error in
        if let data = data {
            let string = String(data: data, encoding: .utf8)!
            print(string)
        }
    }
    task.resume()
    ```

    上面的例子使用了一种叫做 trailing closures 的语法，使得回调函数的函数体可以写在函数的“外面”，详见 [Swift 官方教程](https://docs.swift.org/swift-book/LanguageGuide/Closures.html#ID102)。
