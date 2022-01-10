## 运算相关

- `Int`：整型，在 32 / 64 位机器上分别为 32 / 64 位，如无特殊需求直接使用 `Int` 即可
- `Double`：双精度浮点数
- `Float`：单精度浮点数

这些类型均为 `struct`，也就是说，它们是值类型，传递副本。

`Int` 和 `Double` 都有对应的字面值，字面值与变量是等效的，可以调用类型的方法：

```swift
print(12.negate())            // Int, -12
print(1.2.isEqual(to: 1.2))   // Double, true
```

Swift 没有 `++` 运算符，使用 `+= 1` 代替。

## 字符串

**字符串与字符串字面值**

Swift 中，字符串类型为 `String`，也是 `struct`。

字符串字面值、字符常量均使用两个双引号表示：

```swift
let greeting = "Hello, World!"      // String
let myCharacter: Character = "H"    // Character
```

多行字符串使用前后三个引号包围：

```swift
let softWrappedQuotation = """
The White Rabbit put on his spectacles.  "Where shall I begin, \
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

从行开头到与末尾 `"""` 的第一个引号之间的空白字符将被忽略，如下图所示：

![](../../static/languages/swift/multiline-string.png)

原始字符串（raw string）用 `#" "#` 或 `#""" """#` 包围：

```swift
let regex = #"\d{1,7}"#
```

字符串变量是可以更改的：

```swift
var myString = "Swift"
myString += " Programming Language"
```

**字符串格式化**

Swift 字符串有一种与 Python 中的 f-string 类似的将任意类型变量插入字符串的方法：

```swift
let name = "Jack"
print("Hello, \(name)!")
```

`String` 还提供了 C 风格的格式化构造函数，不过需要注意其占位符与 C 有不同：

```swift
let name = "Jack"
print(String(format: "Hello, %@", name))
```

## 集合类型

### Array

可变数组，相当于 `std::vector`，为**值类型**。

`Array` 类型有两种等价的表示方法，一般使用第一种：

```swift
var rectangles: [Rectangle]
var rectangles: Array<Rectangle>
```

`Array` 有以下几种常见的构造方法：

```swift
let values = [Int]()    // 空数组
let values = [1, 2, 3]
let values = [Int](repeating: 0, count: 3)  // [0, 0, 0]
```

使用 `append`、加法运算符增加元素：

```swift
var values = [Int]()
values.append(1)    // [1]
values += values    // [1, 1]
```

使用 `remove(at:)` 删除元素：

```swift
var values = [1, 2, 3]
let removedValue = values.remove(at: 0) // [2, 3], removedValue == 1
```

使用下标、`first`、`last` 等来索引，通过 `count` 获取数量：

```swift
let values = [1, 2, 3]
print(values[1])    // 2
print(values.first) // Optional(1)
print(values.last)  // Optional(3)
print(values.count) // 3
```

一些常用的对数组进行遍历或者转换的函数：

```swift
let values = [1, 2, 3]
// 遍历
values.forEach { print($0) }              // prints 1, 2, 3
// 遍历，将其元素依次转换
let strings = values.map { String($0) }   // ["1", "2", "3"]
// 通过规则（where 的返回值）过滤
let filteredValues = values.filter(where: { $0 > 1 }) // [2, 3]
// 遍历，将结果累积
let sum = values.reduce(0, { $0 + $1 })   // 0 + 1 + 2 + 3 = 6
```

当然，可以使用 `for` 循环进行遍历：

```swift
for value in values {
    print(value)
}

for index in values.indices {
    print("value at index \(index) is \(values[index])")
}
```

### Dictionary

键值对，对应 `std::map`，值的类型必须相同。

`Dictionary` 为值类型，传递副本。

两种等价的表示方法：

```swift
var grades: [String: Double]
var grades: Dictionary<String, Double>
```

一些常用的构造方法：

```swift
let grades: [String: Double] = [:]  // 空
let grades = [String: Double]()     // 空
let grades = ["Alice": 4.0, "Bob": 3.9]
```

通过键获取元素，类型为 Optional：

```swift
let grades = ["Alice": 4.0, "Bob": 3.9]
grades["Carol"] // nil
grades["Alice"] // Optional(4.0)
```

通过键增加或更改元素：

```swift
var grades = ["Alice": 4.0, "Bob": 3.9]
grades["Carol"] = 3.8
grades["Alice"] = 3.99
```

使用 `for` 循环进行遍历：

```swift
for (name, grade) in grades {
    print("\(name)'s grade is \(grade)")
}
```