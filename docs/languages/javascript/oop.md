## JavaScript 的面向对象

!!! caution "别想学会 JavaScript 的 OOP！"

    JavaScript 的面向对象的实现逻辑并非是类封装逻辑，而是原型链逻辑。最初的 JavaScript 并不支持类语法，但由于原型链实在太过于特立独行，目前 ES6 标准将类语法作为**语法糖**加入了语言标准。这也就是说，JavaScript 的面向对象的底层依然是原型链而非类封装，如果真的想弄明白 JavaScript 的原型链机制，可以查看文档末尾的原型链章节。但对于初学者，我们建议只是用类语法，除非遇到不可解决的 bug，否则不要花时间钻研原型链。

使用类语法的 JavaScript 很大程度上和 C++ 语言类似，这里我们编写一个简易的复数类作为演示，内部已经包含了构造函数、定义成员方法和静态方法等 OOP 基础内容：

```javascript
class Complex {
    constructor(_real, _imaginary) {
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

- 类方法内使用类成员的时候必须使用 `this` 调用
- JavaScript 的所有类成员和类方法均是公有的，JavaScript 不具有访问权限修饰符

### `this` 的指向问题

!!! caution "你的 JavaScript 成人礼"

    `this` 的指向问题是 JavaScript 语言设计里可以说是影响最为深远的的一个缺陷，深远到即使要弄明白这个问题需要花费很长时间，但你不得不投入这些精力，否则你可能会在未来的代码实践中遇到很多 `this` 相关的问题。

    另一方面，即使后来人针对 `this` 指向问题打了很多的补丁，这个问题依然萦绕在前端开发者心中。比如说 React 函数组件支持者的一个很重要的论点就是使用函数组件不会遇到 `this` 指向问题。

严格来说，JavaScript 的 `this` 指向规则只有一条，即 **`this` 永远指向最近的调用者**。观察这样的代码（浏览器运行）：

```javascript
function foo() {
    console.log(this);
}

foo(); // window

let obj = { foo: foo, };
obj.foo(); // { foo: foo }
```

这里两次调用的都是同一个 `foo` 函数，不同的是前者是全局调用（即直接调用），而后者是把函数作为一个对象 `obj` 的属性后通过 `obj` 调用。而 `this` 的指向就是在函数调用的时候确定的。全局调用 `foo` 函数，那 `this` 就指向全局对象，通过 `obj` 调用，那 `this` 就指向 `obj`。

!!! note "全局对象"

    事实上 JavaScript 的全局变量都是全局对象的属性，每一个 JavaScript 运行环境都有一个全局对象。比如说浏览器的全局对象往往是 `window`。你声明的每一个全局变量都会绑定为全局对象的属性（下述代码在浏览器运行）：

    ```javascript
    var a = 1;
    window.a; // 1
    ```

    而在全局环境下调用函数 `foo()`，实际上等价于通过全局对象在调用：

    ```javascript
    foo();
    window.foo();
    ```

    上述两个语句没有差别。

对于更为复杂的对象嵌套，`this` 的指向也遵循着指向最近的调用者的规则：

```javascript
let obj = {
    foo: 1,
    inner: {
        foo: 2,
        fn: function () {
            console.log(this.foo);
        },
    },
};

obj.inner.fn(); // 2

obj.fn = obj.inner.fn;
obj.fn(); // 1

let fn = obj.inner.fn;
fn(); // undefined
```

这里的 `fn` 函数被三个对象分别调用，从上到下为 `obj.inner, obj, window`，可以看出这三次调用中的 `this` 就分别指向这三个对象。

!!! note "一些补充"

    虽说 `this` 的指向规则只有一条，但实际上在写程序的时候，一些函数是比较难弄明白调用者的。比如说传给前端组件的事件监听函数、传给计时器的回调、类函数。这些函数的 `this` 的指向问题实际上已经脱离了本节讨论的范畴，比如说类环境下的 `this` 问题就应该认真学习原型链逻辑才能真正掌握。

`this` 的缺陷就在于其指向是动态的，有些函数可能内部使用了 `this` 但开发者并不知晓，从而在不同情况下应用这个函数的时候会产生不可理解的错误。而现在的一些补丁就是允许开发者硬性指定 `this` 的指向或者永久绑定 `this` 的指向以防止未知错误。

比如说 `call(), apply()` 方法就允许我们手动指定 `this` 的指向，其接受的第一个参数就会成为 `this` 的指向：

```javascript
function foo() {
    console.log(this.a);
}

let obj = { a: 1, };
let another = { a: 2, };

foo(); // undefined
foo.call(obj); // 1
foo.apply(obj); // 1
foo.call(another); // 2
foo.apply(another); // 2
```

而 `bind()` 方法则允许我们将 `this` 的指向永久绑定于某一个对象上。无论后续使用哪一个对象调用这个函数，其 `this` 都保持原先 `bind()` 设定的指向：

```javascript
function foo() {
    console.log(this.a);
}

let obj = { a: 1, };

foo(); // undefined
foo = foo.bind(obj);
foo(); // 1

let another = { foo: foo, a: 2, };
another.foo(); // 1
```

在这些补丁出现之前，为了硬性规定 `this` 的指向，程序员甚至会在函数中使用 `var that = this` 语句先捕获 `this`，之后使用 `that` 代替 `this` 来防止 bug。

而现在更常用的一种解决方法是箭头函数，这种新特性值得专门开出一节来讲讲。

### 箭头函数

这一个章节本应该在函数部分讲解，但是由于箭头函数的出现很大的原因是为了解决 `this` 问题，编者还是将其放到了 OOP 章节。

箭头函数只是函数的另外一种写法：

```javascript
const sum = (x, y) => {
    return x + y;
}

sum(1, 2); // 3
```

同时，对于一些函数体没有中间操作的函数，可以直接在箭头右侧写返回值：

```javascript
const sum = (x, y) => x + y;
sum(1, 2); // 3
```

而在回调函数里面写箭头函数将会让代码更可读：

```javascript
response.data
    .map((val, ind) => ({
        ...val,
        index: ind,
    }))
    .filter((val) => val.name !== undefined)
    .sort((a, b) => a.subscriberNum - b.subscriberNum);
```

唯一要注意的是，如果一个箭头函数直接返回一个对象，这个对象的花括号可能会和代码块的花括号混淆，所以如果直接返回一个对象，记得在对象的花括号外再套一层小括号防止出现 Syntax Error。

箭头函数和 `function` 函数最大不同的地方是其 `this` 的指向跟随上下文的 `this`，也就是说其本身不具有 `this`，在箭头函数体里使用的 `this` 实际上是其上下文（一般是其所在的函数作用域）的 `this`。换句话说，**箭头函数的 `this` 在定义的时候就完成了绑定**，这样的性质就解决了 `this` 指向不明的问题。

考虑这样的代码：

```javascript
const foo = () => console.log(this);

foo(); // window

// Changing caller will not change 'this'
let obj = { foo: foo, };
obj.foo(); // window

// 'call' & 'bind' may fail
foo.call(obj); // window
foo = foo.bind(obj);
foo(); // window
```

可以看出，在全局环境下设定的箭头函数的 `this` 永远跟随全局环境指向全局对象。

但另外一方面：

```javascript
let obj = {
    bar: function () {
        const foo = () => console.log(this);
        return foo;
    },
};

const foo = obj.bar();
foo(); // obj

const bar = obj.bar;
bar()(); // window
```

这里前面获取 `foo` 的时候是使用的 `obj` 调用的普通函数 `bar`，所以 `bar` 的 `this` 指向 `obj`，跟随着的 `foo` 的 `this` 就指向 `obj`。而后面获取的时候 `bar` 是被全局对象调用的，其 `this` 就被自然绑定到了全局对象上，所以内部箭头函数的 `this` 也就绑定到了全局对象上。

!!! note "我该如何使用 `this`"

    实际上只要遵循一定的代码规范（比如说在学习一个前端框架的时候按照其教程和样例代码进行实践）编写代码，`this` 并不会成为一个很大的问题。但是编者依然建议读者应当了解 `this` 的机制，这样才能应对可能出现的意外。
