## if

使用 `if` 进行条件判断，不需要加括号，值必须为 `Bool` 类型：

```swift
if condition {
    // statements
} else if condition2 {
    // statements
} else {
    // statements
}
```

多个需要同时满足的条件可以用 `,` 隔开，与 `&&` 效果相同：

```swift
if condition1, condition2 {
    // executed only if condition1 and condition2 are true
}
```

一个或多个 `if let` 可以和条件语句利用 `,` 结合，只需要写一个 `if`：

```swift
if let data = optionalData,
   let string = String(data: data, encoding: .utf8),
   !string.isEmpty {
    print(string)
}
```

等价于：

```swift
if let data = optionalData {
    if let string = String(data: data, encoding: .utf8) {
        if !string.isEmpty {
            print(string)
        }
    }
}
```

如果成员是 Optional，可以利用一种叫做 optional chaining 的语法进行解包：

```swift
class A {
    var foo: String?
}

class B {
    var a: A?
}

// b 是 B 类型
if let stringCount = b.a?.foo?.count {
    print(stringCount)
}
```

在一条“链” `b.a?.foo?.count` 中，`a` 和 `foo` 都有可能为空，当其中之一为空时，`stringCount` 为 `nil`；当均不为空时，`stringCount` 非空。

## guard

使用 `guard...else` 来处理需要跳出当前作用域的情形。`guard` 后可以是条件表达式，也可以是与 `if let` 类似的对 Optional 的解包：

```swift
func iterate(_ array: [Int]?) {
    guard let array = array else { return }
    // Proceed with a non-nil array
    for element in array {
        print(element)
    }
}
```

`guard` 后必须有 `else` 语句，表示条件不成立（`guard let` 语句中是值为 `nil`）时如何处理并退出。注意，`else` 语句中必须退出当前函数（`return`）、抛出异常或触发严重错误（`fatalError()`）。总之，**不成立时，函数不能够继续执行**。

!!!note "何时使用 `guard`"
    `guard` 语句其实可以用 `if` 语句替代：

    ```swift
    func iterate(_ array: [Int]?) {
        if array == nil { return }
        for element in array! {
            // ...
        }
    }
    ```

    那么，为什么还需要 `guard` 语句呢？
    
    `guard` 语句要求不满足的情况不能继续向下执行，在**编译期**避免了需要立即返回但没有返回的情况，如：

    ```swift
    func unlockPaidItem() {
        let userSubscribed = getSubscriptionStatus()
        guard userSubscribed else {
            showAlert("You have not subscribed yet!")
            // Forget to return here!
        }
        paidItem.unlock()
    }
    // error: 'guard' body must not fall through,
    // consider using a 'return' or 'throw' to exit the scope
    ```

    如果使用 `if`，上面的情况不会产生编译错误，可能要到运行期间出现问题才能发现错误。

## for

[在 Swift 3 中，C 风格 for 循环被删除](https://github.com/apple/swift-evolution/blob/main/proposals/0007-remove-c-style-for-loops.md)，从此 Swift 只支持 `for ... in ...` 类型的 for 循环。

Swift 中范围可以用 `...` 和 `..<` 表示，前者表示闭区间，后者表示前闭后开区间；使用 `stride(from:to:by:)` 以特定步长进行遍历：

```swift
for i in 0...10 {
    // Iterate through 1, 2, ..., 10
}

for i in 0..<10 {
    // Iterate through 1, 2, ..., 9
}

for tickMark in stride(from: 0, to: 60, by: 5) {
    // render the tick mark every 5 minutes (0, 5, 10, 15 ... 45, 50, 55)
}
```

另外，数组等也可使用 `for` 循环进行遍历：

```swift
for value in [1, 2, 3] {
    print(value)
}
```

## while 和 repeat-while

`while` 循环和 C/C++ 相同，`repeat-while` 循环对应 `do-while` 循环：

```swift
while condition {
    // statements
}

repeat {
    // statements
} while condition
```

## switch

`switch` 语句用于匹配值，可以用范围表达式进行范围匹配。

与 C/C++ 不同吗，匹配后跳出 `switch` 不需要 `break` 语句，如果需要跳到下一个 case，使用 `fallthrough`。

```swift
let age = 21
switch age {
case 0:
    print("New born")
case 1..<10:
    print("Child")
case 1..<25:
    print("Young people")
default:
    print("I don't care about it right now")
}

// Young People
```

!!!info
    Swift 中的 `switch` 语句有非常多用法，详见[官方文档](https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html#ID127)。

## 控制转移

在循环中，使用 `continue` 转到下一次循环：

```swift
var characters = ""
for character in "Swift Programming Language" {
    if character == " " {
        continue
    }
    characters.append(character)
}
```

使用 `break` 退出循环：

```swift
for character in "Swift Programming Language" {
    if character == " " {
        print("Blank character exists!")
        break
    }
}
```

使用标签跨越多层循环进行控制：

```swift
let words = ["Swift", "烫", "Programming", "Language"]
outerLoop: for word in words {
    // A stupid switch just to demostrate
    switch word.first {
    case "S": print("Swift")
    case "P": print("Programming")
    case "L": print("Language")
    default:
        print("Invalid word!")
        break outerLoop
    }
}

// Swift
// Invalid word!
```
