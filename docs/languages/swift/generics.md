泛型对应 C++ 中模版的概念，函数、类型和协议均可进行泛型编程：

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T)
struct Array<Element> {...}
protocol View {
    associatedtype Body: View
}
```

## 函数泛型

在函数名后加上 `<类型参数>`，参数可有一个或多个：

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

!!!info
    `inout` 关键字用于函数参数，表示传递引用而非值，详见 [官方教程](https://docs.swift.org/swift-book/LanguageGuide/Functions.html#ID166)。

调用时，由于 Swift 有自动类型推断，不需要显式写出类型：

```swift
var a = 1
var b = 2
swapTwoValues(&a, &b)
```

## 类型泛型

在类型名后加上 `<类型参数>`，如之前介绍的 `Optional`：

```swift
enum Optional<Wrapped> {
    case some(Wrapped)
    case none
}
```

再如，`Array` 等内置类型：

```swift
struct Array<Element> {
    ...
}
```

## 协议泛型

协议的泛型与前两类有所不同，需要在协议体内用 `associatedtype` 声明：

```swift
protocol Sequence {
    associatedtype Element
}
```

某个类型遵循协议时，类型声明中需要说明协议内所有 `associatedtype` 的真实类型：

```swift
struct MyIntArray: Sequence {
    typealias Element = Int
}
```

在这里，我们使用 `typealias` 指出了 `Sequence` 中 `Element` 的类型为 `Int`。

!!!note typealias
    `typealias` 关键字用来给类型赋予别名，类似于 C 中的 `typedef` 和 C++ 中的 `using`。

## 类型约束

定义泛型时，可以对类型作一定约束，使得我们能够获得关于模版类型的更多信息。比如之前提到的 `Equatable`：

```swift
func isEqual<T: Equatable>(lhs: T, rhs: T) -> Bool {
    return lhs == rhs   // equatable
}

struct EquatableArray<Element> where Element: Equatable {...}
struct EquatableArray<Element: Equatable> {...}

protocol EquatableSequence {
    typealias Element: Equatable
}
```

通过限制 `T` 的类型为 `Equatable`，我们的 `isEqual` 得以调用 `==` 运算符判断是否相等。
