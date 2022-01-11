我们之前介绍的都是一些非常简单的情况：它们都不涉及变化的状态。然而，几乎所有的应用都需要依赖于变化的数据。我们需要一种方法，使得当数据更新时及时、正确地更新我们的 UI。我们将介绍 UI 与数据绑定过程中涉及的一些最基本的常见数据流。

## State

我们来看一个简单的例子。在我们的 View 中，需要记录用户点击的次数，并显示在屏幕上。你可能想要这样写：给 `ContentView` 加上一个变量 `count`，然后在 `Button` 中更改这个值：

```swift hl_lines="2 6"
struct ContentView: View {
    var count: Int = 0

    var body: some View {
        HStack {
            Button("Click") { count += 1 }
            Text("\(count)")
        }
    }
}
```

但这样是不能正常工作的。

首先，这个代码并不能通过编译：

```
Left side of mutating operator isn't mutable: 'self' is immutable
```

原因是，`struct` 是值类型，一般的函数不能够修改其变量的值。

而且，有一个更加深层的原因。我们改变了 `count`，并且希望 `Text("\(count)")` 能显示最新的值。但是，`ContentView` 并不知道当 `count` 更新时需要更新 UI。

修改的方法很简单，只需要在 `var count: Int = 0` 前加上 `@State`：

```swift
@State var count: Int = 0
```

这样，就能够通过编译，并且我们的界面会随着 `count` 的更新而更新了：

![](../../static/frontend/swiftui/data-flow-state-1.gif)

**`@State` 是什么语法？为什么能够修改 `count`？**

`@State` 涉及到一种叫 [property wrapper](https://docs.swift.org/swift-book/LanguageGuide/Properties.html#ID617) 的语法。简单来说，property wrapper 有如下特性：

- 是一个包装后的类型，但对外的表现就像是被包装前的数据一样
- 当被包装的值被设定或被读取时，执行特定的代码

因为我们实际上是修改 `@State` 所包装的值而非 `@State` 变量本身，所以是可以修改 `count` 的值的。

**为什么更新 `count` 之后 UI 会自动更新？**

上面关于 property wrapper 的特性具体到 `@State`，即：

- 我们可以直接取其所储存的值（如上面的 `Text("\(count)")`）
- 它在被设定时会触发 View 重新计算 `body`（如上面的 `count += 1`）

于是，通过 `@State`，我们实现了 **UI 与数据的绑定**，只要 `count` 更新，整个 View 就会被重新计算。我们的 UI 在任何时刻都反映了数据最新的值，我们只需要修改数据，而无需手动地更新 UI。

!!!note "React 中的类似方法"
    如果你对 React 有所了解，`@State` 相当于：

    - 对于 `Component`：通过 `state` 获取值；调用 `setState` 更改值
    - 对于纯函数组件：`useState` 的两个返回值，分别对应获取和更改

    实际上，View 和 React 中的纯函数组件概念上是一样的。SwiftUI 通过 Swift 的 property wrapper 语法，将获取值、设定值 + 更新 UI 简化为一个变量的读取和设定，而 React 因为语言的限制，设定值 + 更新 UI 只能采用调用函数的方法。

## Binding

`@State` 解决了在**单个** View 中根据数据更新 View 的问题。但是，有时我们为了重用代码或保持代码的简洁，需要将一些 View 拆分出来，这就产生了一个问题：**如何在父 View 和子 View 中共享一个可由子 View 修改的值？**也就是说，数据如何从子视图流向父视图？

比如，我们希望把「点击一次使得值加 1」的按钮封装起来，以便重复使用，那么上面的 `Button` 不再在 `ContentView` 中定义，而是在另一个 View 中出现：

```swift
struct IncrementButton: View {
    // TODO: Data
    var body: some View {
        Button("Increase") {
            // TODO: Increse data
        }
    }
}
```

如何在这两个 View 共享 `count` 使得 `IncrementButton` 可以修改呢？这就需要 `@Binding`。在 `IncrementButton` 中，添加一个成员 `@Binding var value: Int`，并在 `Button` 的 `action` 中将其值加 1：

```swift hl_lines="2"
struct IncrementButton: View {
    @Binding var value: Int

    var body: some View {
        Button("Increase") { value += 1 }
    }
}
```

而在父 View 中，通过在 `@State` 变量名前加上一个 `$` 来传递 `Binding`：

```swift hl_lines="6"
struct ContentView: View {
    @State var count: Int = 0

    var body: some View {
        HStack {
            IncrementButton(value: $count)
            Text("\(count)")
        }
    }
}
```

在 `IncrementButton` 中修改 `value`，`ContentView` 中的 `count` 就会同步变化。换而言之，我们实现了**父 View 和子 View 数据的绑定。**

!!!note "关于 `$count`"
    在变量名前加 `$` 并不是一种语法，而是编译器自动给 `ContentView` 加上了一个变量名为 `$count` 的成员变量，它的类型为 `Binding`。

!!!note "更多的原生 View"
    掌握了 `@Binding` 后，你可以使用更多的与数据相关的原生 View 了。比如，一个文本框：

    ```swift
    TextField("", text: $myText)
    ```

    当用户输入文本时，`TextField` 中的文本会同步到 `myText` 中。

    你可以在文档中探索更多的原生 View。

???note "`$count` 的实质"
    在文档中，你可以看到 `Binding` 有这样一种构造函数：

    ```swift
    init(get: @escaping () -> Value, set: @escaping (Value) -> Void)
    ```

    第一个参数 `get` 用于返回 `Binding` 变量读取的值，第二个参数 `set` 是当 `Binding` 变量改变时所执行的操作。

    因此，你完全可以用这种方法取代 `$count`：

    ```swift
    IncrementButton(value: 
        Binding(
            get: { count }, 
            set: { count = $0 } 
        )
    )
    ```

    当 `IncrementButton` 获取 `value` 时，调用 `get` 获取当前的值；当 `IncrementButton` 改变 `value` 时，调用 `set` 更新 `count`。由 `@State` 生成的 `Binding` 变量无非就是实现这样的功能。

## UI 是状态的函数

通过上面的一些例子，你应该能总结出 SwiftUI 中 View 的一些特点。最明显的是：**View 唯一地由数据确定。**如果将 `@State`、`@Binding` 所代表的数据视作一种“状态”，那么我们可以写出这样一条式子：

```
View = f(state)
```

其中的 `f`，其实就是 `View` 中的 `body`。每次 `state` 更新时，View 通过 `body` 的定义计算出新状态下的 UI，并进行更新。所以，这也是为什么 `body` 应当是一个**计算变量**，而不是储存变量——它本质上是一个函数。

这种性质一个很自然的结果是：**我们无法获得 View 的引用，也无法直接修改其 UI，所有对 UI 的修改都源自于数据的改变**。

我们在下一节中进行更深入的讨论。
