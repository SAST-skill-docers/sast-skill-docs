## 变量

用 `var` 声明可更改的变量，`let` 声明不可更改的变量（常量）。

声明变量时，在变量名后用 `: Type` 声明类型：

```swift
var myVariable: Int = 42
myVariable = 50
let myConstant: Int = 42
```

如果上下文明确，编译器能够自动确定变量的类型，那么类型声明可以省略（比如，`42` 是整数字面值，自动推断为 `Int` 类型）：

```swift
let myInt = 42 // Int
let myString = "Hello" + ", World!" // String
let myDouble = 42.0 + 1 // Double
```

需要注意的是，Swift 不允许隐式类型转换，进行类型转换只能通过目标类型的构造函数进行：

```swift
let intValue = 12 // 类型为 Int
let uint64Value = UInt64(intValue)  // 类型为 UInt64

// let uint64Value: UInt64 = intValue
// error: cannot convert value of type 'Int' to specified type 'UInt64'
```

`var` 和 `let` 声明的变量在使用之前**必须初始化**，否则编译不通过。不同之处在于，`var` 可以多次赋值，而 `let` 只能赋值一次。

Swift 支持空值（Optional），表示变量的值“不存在”。在类型后加上 `?` 声明可空的变量，关键字 `nil` 表示值为空：

```swift
var result: Int? = nil
print(result) // nil

result = 1
print(result) // Optional(1)
```

注意到，即使 Optional 值不为空，Optional 与原来的类型是不同的两个类型，不能直接获取其值。

Optional 的值有两种读取方法。第一种是当确保 Optional 不为空时，在变量名后加上 `!` 表示强制解除包装获取值。当值为空时，使用 `!` 会导致运行错误，程序直接崩溃：

```swift
if result != nil {
    print(result!)
}

result = nil
print(result!)
// Fatal error: Unexpectedly found nil while unwrapping an Optional value
```

另一种是建议使用的方法，通过 Swift 的一个语法糖 `if let`（或 `if var`）实现：

```swift
if let realResult = result {
    // 此时 realResult 类型为 Int，不是 Optional<Int>
    print(realResult)
}
```

相当于

```swift
if result != nil {
    let realResult = result!
    print(realResult)
}
```

如果 `if let` 大括号内不需要再引用原来的 Optional，一般 `if let` 语句 `let` 后面新的变量名直接使用原有的变量名，省去重复命名的困扰：

```swift
if let result = result {
    // 此时 result 类型为 Int，存在于括号作用域中
    print(result)
}
```

另外，可以使用 `??` 给 Optional 提供默认值，当值为空时，使用默认值：

```swift
print(result ?? 0)
```

与 Optional 有关的更多条件判断语句将在后面介绍。

## 函数

使用关键字 `func` 声明函数。函数参数的声明与变量声明类似，需要显式指出类型；返回值用 `-> Type` 显式指出；可以为参数指定默认值：

```swift
func add(a: Int, b: Int = 2) -> Int {
    return a + b
}
```

如果函数体只有一行，可以省略 `return`：

```swift
func add(a: Int, b: Int = 2) -> Int {
    a + b
}
```

调用时，需要写出标签：

```swift
add(a: 1, b: 1)
```

Swift 函数声明的特点是，其参数有两个“名称”：参数名（函数体内部使用）和标签（调用时指出），按照 `标签 参数名: 类型` 的形式声明。如果不区分标签和函数名，则使用 `参数名: 类型` 声明。

对于上面的 `add`，参数只有名称，`a` 和 `b` 既作为参数名称，又作为标签。与之对应，有时候我们将标签和参数名分离，如 `Array` 的 `remove` 方法：

```swift
func remove(at index: Int) -> Element {...}
myArray.remove(at: 0)   // at 为标签，调用时使用
```

调用函数时，必须写出标签，因此上面的调用不能写成 `add(1, 1)`。如果需要省略标签，在标签的位置使用下划线 `_` 替代：

```swift
func add(_ a: Int, _ b: Int) -> Int {...}
add(1, 2)
```

!!!note
    Swift 的标签和参数名的设计有以下几点好处：

    **1. 需要明确写出标签，使得调用时语义更加明确。**

    C 风格函数调用时没有标签，各参数的顺序可能会造成困扰，比如 `memcpy(buf1, buf2)`，究竟哪个是 `src`，哪个是 `dest`？

    而 Swift 中可以利用标签来明确：

    ```swift
    copyMemory(source: buffer1, dest: buffer2)
    ```

    **2. 标签增强代码可读性，同时参数名保持应有的含义**

    还是上面的例子。我们可以将 `copyMemory` 的标签和参数名分离：

    ```swift
    func copyMemory(from source: SomeType, to dest: SomeType) {
        print("Copying from \(source) to \(dest)")
        ...
    }

    copyMemory(from: buffer1, to: buffer2)
    ```

    这样调整之后，函数调用 `copyMemory(from: buffer1, to: buffer2)` 就可以用英文从左往右读出来：“Copy memory from buffer 1 to buffer 2”。这是 Swift 的[三个特点](https://www.swift.org/about/)之一：expressive。

    同时，函数体内部引用两个参数时仍然保持 `source` 和 `dest` 的语义，避免了 `print("Copying from \(from) to \(to)")` 这样的情形。

    关于标签和参数名的使用规范，详见 Swift 的 [API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/)。
