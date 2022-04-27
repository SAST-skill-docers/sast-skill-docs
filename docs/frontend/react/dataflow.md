# 非常规数据流

我们先前介绍过 React 的瀑布数据流，也就是父组件向子组件传递数据的数据组织形式。但是有时我们会要求子组件的行为影响父组件，或者多个组件之间共享信息。本部分将会简要介绍如何实现这些非常规数据流。

## 反向数据流

反向数据流实际上是一个很常见的需求，比如说**分页器**。分页器在 UI 构成上而言，仅仅是一个网页的部分，但是它的状态却决定了整个网页应当显示的内容。从而我们必须构建从分页器向父组件的数据流，让分页器起到作用。

这里我们沿用先前的代码，考虑一个简化的反向数据流需求。比如说这里，我们希望让点击正方形这个事件，不仅仅改变正方形本身的颜色，还可以改变父组件 `App` 内部的计数器来显示点击次数。

!!! note "删掉一些多余的代码"

    这里我们不涉及生命周期，故可以删除掉 `Square` 组件内的计时器以及相关的生命周期代码。

首先我们需要在父组件 `App` 的状态中添加一个计数器，并且在构造函数中初始化。修改后的 `App` 组件为：

```typescript
// Component 'App'
interface AppState {
    readonly clickCnt: number;
}

class App extends React.Component<Record<string, never>, AppState> {
    constructor(props: Record<string, never>) {
        super(props);
        this.state = { clickCnt: 0 };
    }

    render(): ReactElement {
        return (
            <div>
                <p>This is a square!</p>
                <p>We have click it for {this.state.clickCnt} time(s)!</p>
                <Square size={100} color="red" />
            </div>
        );
    }
}
```

??? note "`Record<string, never>` 是什么？"

    `Record<string, never>` 可以理解为空对象 `{}` 的类型标注，这里仅仅是为了通过 TypeScript 的类型检查。

此外，我们还需要编写一个递增计数器的成员函数：

```typescript
// Component 'App'
increaseCnter(): void {
    this.setState((prev) => ({
        ...prev,
        clickCnt: prev.clickCnt + 1,
    }));
}
```

现在我们需要思考的就是如何让子组件 `Square` 调用到这个函数。我们注意到在 TypeScript 语言之中，函数也是一种对象，所以我们完全可以通过组件属性将这个函数传递给子组件。那么我们需要在子组件属性接口上增添一个回调函数：

```typescript hl_lines="4"
interface SquareProps {
    readonly size: number;
    readonly color: string;
    readonly increaseCnter: () => void;
}
```

之后通过组件属性将父组件成员函数向下传递：

```typescript
// Component 'App', Function 'render'
<Square size={100} color="red" increaseCnter={() => this.increaseCnter()} />
```

然后我们就可以在子组件内通过 `this.props.increaseCnter` 来调用这个函数了：

```typescript
// Component 'Square', Function 'render'
render(): ReactElement {
    return <div
        style={{
            width: this.props.size,
            height: this.props.size,
            backgroundColor: this.state.color
        }}
        onClick={() => {
            this.switchColor();
            this.props.increaseCnter();
        }}
    />;
}
```

这样我们就可以通过记录点击正方形的次数了。

!!! note "真的是反向数据流吗？"

    我们通过传递回调函数实现了反向数据流，但实际上这样的操作并没有违反瀑布数据流的约定。父组件向下传递的，仅仅是子组件在约束下操作父组件状态的权限，实际上还是可以理解为父组件控制了子组件的行为边界。

## 组件间共享数据

我们注意到即使我们可以通过传递回调函数的方式实现反向数据流，但实际上这种反向数据流依然只能在父子之间传递。对于更大规模的前端，我们往往需要跨越文档树传递信息，乃至整个应用都需要共享某些信息。此时依然通过回调函数传递就显得极为局限，而现在的一种通用解决方案为 Redux。

但由于 Redux 一般用于较大型的工程，这里使用小正方形作为 DEMO 难以讲解。所以这里提供若干学习 Redux 的一些资料链接，希望读者在使用到 Redux 的时候可以参考：

- Redux 官方文档 <http://cn.redux.js.org/introduction/getting-started>
- 阮一峰 Redux 教学文档 <https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html>

!!! note "预留文档"

    后续可能会编写 Redux 教学文档。