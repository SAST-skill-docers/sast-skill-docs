# 组件、属性与状态

## 组件 & `render` 函数

React 的一个核心概念就是**组件**。

组件更像是将 UI 拆分得到的独立可复用的小块，其能够接受属性的传入，并且返回出描述屏幕上的展示内容的 React 元素。

我们将会从类组件入手介绍 React 组件的编写方式。一个经典的类组件的结构如下（即下发框架之中的 `App.tsx` 文件内容）：

```typescript
class App extends React.Component {
    render(): ReactElement {
        return <p> Hello, React! </p>;
    }
}
```

每一个类组件都是 `React.Component` 类的子类，其中我们需要重载 `render` 函数以定义这个组件在屏幕上的展示方式（用户界面）。`render` 函数的返回值为使用标签语法编写的 `ReactElement`。比如说上述 `App` 组件就返回了一句问候语，这就代表 `App` 组件在实例化的时候会渲染出这一句问候语。

!!! note "JSX 语法拓展"

    React 对 JavaScript 语言作了一定的语法拓展，拓展后的 JavaScript 称为 JSX。JSX 和原生 JavaScript 的最大差别就是引入了标签语法：

    ```javascript
    const p = <p>Hello world!</p>;
    ```

    这类类似于 HTML 标签的语法定义了一个对象，其等价于以下表达：

    ```javascript
    const p = React.createElement(
        "p",
        undefined,
        "Hello world!"
    );
    ```

    JSX 的标签对象中允许嵌入 JavaScript 表达式，其会运算出其值后转为字符串嵌入：

    ```javascript
    const name = "Adam";
    const p = <p>Hello, {name}!</p>;
    ```

    转化为字符串的目的是防止 XSS 攻击。

    此外，JSX 的标签语法和 HTML 类似，允许定义各种属性，这些特性以后会讲到。

    另外，本文档基于 TypeScript 的标签化拓展 TSX 语言，其标签语法基本和 JSX 一致。

实例化一个组件也是简单的，类似 HTML 语法。比如下面都是合法的实例化 `App` 组件的语句：

```typescript
<App />;
<App title="SAST-React" />; // 'props' will be introduced later
<App><div /></App>;
```

!!! note "`index.tsx` 文件的作用"

    我们下发的框架的 `src` 目录下仅仅有 `App.tsx` 和 `index.tsx` 两个文件。其中我们已经介绍了 `App.tsx` 文件的作用，其定义了一个类组件，并且规定了这个组建的显示方式。

    而 `index.tsx` 文件的作用就是将定义好的类组件渲染到 DOM 树上。我们观察其文件内容：

    ```typescript
    const root = ReactDOM.createRoot(
        document.getElementById('root') as HTMLElement
    );
    root.render(<App />);
    ```

    第一个语句的含义为在 DOM 树上寻找 ID 为 `root` 的元素，并将其设定为 React 应用的根元素。而如果打开 React 应用 `public` 文件夹下的 `index.html` 文件，我们就可以找到这个根元素（其一般位于第 31 行左右）：

    ```html
    <div id="root"></div>
    ```

    而第二个语句就是将 `<App />` 这个组件渲染到获取到的根元素上。

    所以 `index.tsx` 的作用就是搭建起传统 HTML 和 React TSX 之间的桥梁，让我们可以自由地使用 TSX 语法编写组件，而将这些组件转换到 HTML 的任务则交给了 React 框架。

## 编写第一个自定义组件

我们说过组件是独立可复用的，也就是说我们可以编写自己的组件并使用。比如说我们可以使用类组件写一个渲染一个红色正方形的组件 `Square`（直接在 `App.tsx` 文件内插入下述代码即可）：

```typescript
class Square extends React.Component {
    render(): ReactElement {
        return <div style={{width: 100, height: 100, backgroundColor: "red"}} />;
    }
}
```

这个时候我们就可以在 `App` 组件的 `render` 函数里面使用这个组件了：

```typescript
// Component 'App'
render(): ReactElement {
    return (
        <div>
            <p>This is a square!</p>
            <Square />
        </div>
    );
}
```

编译之后就能看到屏幕上的一个红色正方形。

## 自定义组件的状态（传入 `props`）

刚刚编写 `Square` 的时候应该注意到了，正方形的尺寸和颜色都是写死的，实际上我们可以通过传入属性的方式完成对组件形态的控制，正如 HTML 标签的属性。

我们在实例化 `Square` 的时候可以如下面代码一样传入若干的属性：

```typescript
// Part of 'render' function in Component 'App'
<Square size={100} color="red" />
```

那么这些属性我们怎么在组件内部捕获呢？答案是通过 `this.props` 变量：

```typescript
// Component 'Square'
render(): ReactElement {
    return <div
        style={{
            width: this.props.size,
            height: this.props.size,
            backgroundColor: this.props.color
        }}
    />;
}
```

但你会发现报错，基本的意思就是 `props` 上没有成员 `size, color`。这实际上就是 TypeScript 的要求，你需要为组件指定这个组件需要哪些属性，哪些属性是可选的等等，这样可以有效避免出现难以探查的 `undefined` 的问题。

指定的方式是 `interface`，你需要在这个接口之中定义这个组件接受的所有属性。比如说 `Square` 只需要接受尺寸和颜色：

```typescript
interface SquareProps {
    size: number;
    color: string;
}
```

这里也可以指定部分属性是可选的，这样的话实例化组件就没必要传入这一部分属性，并且通过 `this.props` 访问这些属性会得到 `undefined` 。但是这样的行为显然更为可控。

我们在 [TypeScript 文档](/languages/typescript) 中提到过接口的属性除了可以规定为可选属性，还可以规定为只读属性。实际上 React 严格要求属性是不可更改的，这种要求类似于纯函数，纯函数的参数传入之后不可修改，至少不会影响到原变量。

所以如果严格要求的话，接口定义应该写为：

```typescript
interface SquareProps {
    readonly size: number;
    readonly color: string;
}
```

如果不写的话可能会在部分较为严格的条件下编译失败。

之后将其挂载到组件的定义上，挂载的语法类似于 C++ 的模板语法：

```typescript
class Square extends React.Component<SquareProps> { // Register 'props' here
    // ...
}
```

这样你就基本了解了如何给组件传入属性。不过现在你应该已经注意到了属性是从父组件向子组件传递的，这也是后续会提到的**瀑布数据流**，也就是说这些控制组件形态的属性数据的流向永远是向下的。

## 实现动态的 UI（添加组件的 `state`）

我们已经提到了使用 `props` 来自定义组件的形态，但根据 `props` 的不可变性就可以知道只使用 `props` 控制的组件等价于纯函数，也就是说其渲染的 UI 是完全静态的，只是单纯地简化了一些代码写法而已。

为了实现动态的效果，比如最简单的点击变色，就需要让组件具有一定的记忆功能，让组件管理自己的状态，这就是 `state`。

就和类成员一样，组件的 `state` 是每一个实例化的对象所管理的一些变量，这些变量可能需要通过父组件初始化，但是这之后这些 `state` 就完全受控于管理它们的组件本身。

比如说我们想让正方形的颜色不受控于父组件 `App`，那么我们可以设立 `state` 来管理。给组件创建状态的方式是简单的，我们需要编写一个自定义的构造函数：

```typescript
// Component 'Square'
constructor(props: SquareProps) {
    super(props); // Never forget this when writing constructor!
    this.state = { color: "red" };
}
```

之后在渲染函数之中就可以将合适的 `this.props` 替换为 `this.state`，这就意味着一部分渲染结果由组件本身状态接管，不受控于父组件传入的属性。比如说这里，正方形尺寸本身还是受控于父组件，但是颜色由自身控制：

```typescript
// Component 'Square'
render(): ReactElement {
    return <div
        style={{
            width: this.props.size,
            height: this.props.size,
            backgroundColor: this.state.color
        }}
    />;
}
```

变更当前组件的状态使用的函数为 `this.setState`，这个函数由 `React.Component` 实现，可以直接继承使用。

**一定注意，不要使用 `this.state = ...` 这样的赋值句改变状态，因为 `this.setState` 内部除了修改状态还包括触发再次渲染等等过程，具体流程见下面的讲解。**

修改状态的方式为：

```typescript
this.setState({ ...this.state, color: "blue" });
```

这个语句就会把状态之中的 `color` 属性修改为 `blue`。

!!! note "修改组件状态的时候 React 在做什么"

    如果我们修改了某一个组件的状态，实际上就可能会影响到这个组件在页面上的显示，比如说上述代码可以修改正方形的颜色。

    那么为了响应这次更改，我们在修改完组件状态后应当立刻根据新状态重新渲染这个组件。但是我们注意到这个组件的状态可能通过属性传入了子组件之中，这意味着该组件的子组件也需要修改。

    所以说每当我们调用 `this.setState` 函数，就会触发以该组件为根的所有组件的重新渲染，进而触发页面的刷新。
    
    这也就说明了，我们不能直接使用 `this.state = ...` 这类赋值语句直接修改状态，否则可能导致组件数据和显示方式不一致。

为了让用户可以调用到这个函数，我们可以将这个函数绑定成点击正方形时的回调，这样用户点击正方形的时候就可以触发这个函数，修改正方形颜色：

```typescript
// Component 'Square'
render(): ReactElement {
    return <div
        style={{
            width: this.props.size,
            height: this.props.size,
            backgroundColor: this.state.color
        }}
        onClick={() => this.setState({ ...this.state, color: "blue" })}
    />;
}
```

现在我们希望让用户点击正方形的时候，正方形颜色在红色和蓝色之间相互切换。那么我们可以首先编写一个切换颜色的函数，内部调用 `this.setState()` 方法：

```typescript
// Component 'Square'
switchColor(): void {
    this.setState((prev) => ({
        ...prev,
        color: prev.color === "red" ? "blue" : "red",
    }));
}
```

这里注意，我们在 `this.setState` 函数中传入了一个回调函数，原因见下述解释。

!!! note "状态的时间依赖性"

    出于性能方面的考量，`this.setState` 实际上设计为一个异步函数，也就是说状态的修改并非是阻塞的、立即的。这产生了下述的问题：

    - 在 `this.setState` 调用后立刻访问 `this.state` 可能出错
    - 如果新状态依赖于上一次的状态，也有可能出错

    不过这两个问题，React 框架都做出了回答。

    第一个问题有两个解决方案，一个是给 `this.setState` 的第二个参数传入一个回调函数，该回调函数将会在状态更新完毕后执行：

    ```typescript
    this.state({
        ...this.state,
        data: "*".repeat(114514).split("*"), // A large array updated in setState
    }, () => {
        console.log("State updated!");
    });
    ```

    第二个方案为使用 `componentDidUpdate` 生命周期方法，该方法将会在下一部分介绍。

    第二个问题的解决方案则是给 `this.setState` 的第一个参数传入回调函数，该回调函数接受的参数为旧状态，返回值将会被设定为新状态。在类似于计数器递增、状态机切换状态等场景中，使用回调函数作为第一个参数是更为合适的选择。这里的代码示例见上述 `switchColor` 函数。

属性也应当使用接口定义并且在组件定义上体现，否则可能无法通过编译：

```typescript
interface SquareStates {
    readonly color: string;
}

class Square extends React.Component<SquareProps, SquareStates> { /* ... */ }
```

!!! note "瀑布数据流"

    瀑布数据流形容的是 React 框架中数据流的管理方式，描述的是 React 组件间的数据仅存在从上至下的流动。

    `props` 可以认为是一股从上而下的水流，上游发生的变化会立刻反映到下游。React 中父组件通过 `props` 规定子组件的行为，而 `props` 的改变就意味着子组件需要立刻重新渲染。

    `state` 可以认为是某处的另外一个水源。React 允许组件管理自身的状态，也允许 `state` 的数据通过 `props` 向下传递。

    当然，瀑布数据流并不是绝对的，我们后续会提到通过回调函数实现数据的向上流动，以及也会简单介绍通过 Redux 来实现跨组件数据共享的方法。
