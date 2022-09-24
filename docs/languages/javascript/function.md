## JavaScript 的函数

### 函数声明和调用

我们一般把 JavaScript 的函数也认为是一种变量，因为在 JavaScript 之中，函数的行为很大程度上和变量类似。

JavaScript 语言之中声明一个函数的语法为：

```javascript
function sum(x, y) {
    return x + y;
}
```

而另外一种声明方式如下，这种方式的内涵就是把函数当成一种变量：

```javascript
const sum = function (x, y) {
    return x + y;
};
```

这里 `sum` 作为一个变量，它的类型并非前文所提过的基本类型，因此也是一个对象，它的构造函数是 `Function`。

函数调用的语法则和 C/C++ 语言一致：

```javascript
sum(1, 2); // 3
```

!!! note "无用的参数列表"

    实际上 JavaScript 从来不检查你函数调用是不是符合参数列表，本身 JavaScript 就不检查变量类型，实际上 JavaScript 还不检查你函数调用的时候传入的参数个数。比如说你给 `sum` 函数传入一个参数还是三个参数，其都正常工作：

    ```javascript
    sum(1); // NaN
    sum(1, 2, 3); // 3
    ```

    其处理逻辑为**声明了却没有传入的参数当成 `undefined`，多余的参数则需要手动捕获**。也就是说 `sum(1)` 在执行的时候，参数 `x` 是 `1`，而参数 `y` 是 `undefined`，结果自然是 `NaN`。

    而捕获多余参数的方法有两种，一种是在参数列表中加入可变长参数 `...rest`。不过注意，可变长参数会被赋值为一个数组，并且在没有多于参数的时候被赋值为**空数组**而非 `undefined`：

    ```javascript
    function sum(x, y, ...rest) {
        let s = 0;
        for (let i = 0; i < rest.length; ++i) s += rest[i];
        return x + y + s;
    }
    ```

    另外一种是在函数之中使用 `arguments` 变量，这个变量存储了函数所接受的所有参数，并将其包装为一个数组。所以理论上甚至都不需要声明参数列表：

    ```javascript
    function sum() {
        let s = 0;
        for (let i = 0; i < arguments.length; ++i) s += arguments[i];
        return s;
    }
    ```

!!! caution "消失的返回值"

    JavaScript 不强制要求语句以分号结尾，而其引擎会在每一行没有以分号结尾的语句后面自动添加分号。但是，这可能会导致一些意想不到的结果。

    部分 C/C++ 程序编写者可能在返回值较为庞大的时候将返回值换行写，这样美观度较好，但是在 JavaScript 语言中，这样很可能出现问题：

    ```javascript
    function foo() {
        return
            ({ name: "foo", });
    }
    ```

    实际上调用函数得到的返回值是 `undefined`。这个 bug 的原因在上面已经解释了，因为 `return` 语句单独成一行，所以 JavaScript 引擎会给 `return` 后面加一个分号，从而上述代码实际上是：

    ```javascript
    function foo() {
        return;
            ({ name: "foo", });
    }
    ```

    这个函数就没有返回值了（也就是返回值是 `undefined`）。

    正确写法应当是：

    ```javascript
    function foo() {
        return {
            name: "foo",
        };
    }
    ```

    这个时候 JavaScript 引擎并不认为 `return {` 是一个完整语句，所以不会在其末尾添加分号。

??? note "变量提升"

    我们说过 JavaScript 由于工期短，具有一些设计上的缺陷（一部分已经转变为语言特性）。变量提升就是这些缺陷之一，其关系到 JavaScript 的变量作用域问题。

    C/C++ 语言使用大括号代码块 `{...}` 来标记变量作用域，这被称为**块作用域**。但最初始的 JavaScript 并不具有块作用域，其只具有**函数作用域**。也就是说，控制语句块 `for, if` 等等都不会形成作用域，真正限定变量作用于的是函数：

    ```javascript
    // i is *not* visible here 
    // j is *not* visible here 
    function test() {
        // i *is* visible here 
        // j *is* visible here

        var i;
        for (var j = 0; j < 10; ++j) {
            // i *is* visible here 
            // j *is* visible here
        }

        // i *is* visible here 
        // j *is* visible here
    }
    // i is *not* visible here 
    // j is *not* visible here 
    ```

    这种作用域形成的原理是 **JavaScript 语言会将每一个变量的声明提升到其所属函数最顶部，原先的声明语句变为赋值语句**，当然在所有函数外声明的变量就会具有全局作用域。

    比如下面的代码：

    ```javascript
    var name = "Mike";
    function foo() {
        console.log(`Hello, ${name}!`);
        if (true) {
            var name = "Helen";
        }
    }
    foo();
    ```

    这段代码的执行结果为 `"Hello, undefined!"`。

    要理解为什么，我们首先需要手动帮 JavaScript 做一步变量提升。注意到这里有两个声明语句，那么我们将其声明提升到函数最顶部，原先的声明改成赋值：

    ```javascript
    var name;
    name = "Mike";
    function foo() {
        var name;
        console.log(`Hello, ${name}!`);
        if (true) {
            name = "Helen";
        }
    }
    foo();
    ```

    这样就不难理解 `undefined` 出现的原因了。

    事实上不仅是变量，函数也会被提升。比如说：

    ```javascript
    greet(); // "Hello!"
    function greet() {
        console.log("Hello!");
    }
    ```

    这个程序会正常输出 `"Hello!"`。函数提升的特点是其会将函数体跟随着向上提升，而变量提升并不会把其初始值跟随着提升。

    但另外一个方面，如果我们使用类似声明变量的方式声明函数，**其函数体不会跟随着提升**：

    ```javascript
    greet(); // Uncaught TypeError: greet is not a function
    var greet = function () {
        console.log("Hello!");
    }
    ```

    之后再补充一点有关提升顺序的规则，就是**变量和函数同时提升的时候，变量声明按照声明顺序整体放在作用域最开头，之后函数声明按照声明顺序整体紧接着变量声明整体，按照变量形式声明的函数当作变量声明**，以及**如果某一个符号同时用在多个变量声明中，视作一次声明。用在多个函数声明中，后者覆盖前者。同时用在函数和变量声明之中，采用函数的声明**。比如：

    ```javascript
    console.log(f); // function f() {}
    function f() {}
    var f = 3;

    console.log(f);  // function f() {}
    var f = 3;
    function f() {}
    ```

    这里无论如何调整顺序，函数的声明会覆盖变量的声明。

    ---

    其实关于 JavaScript 变量作用域的问题，还有一个值得注意的是**隐式全局变量**问题。如果不使用任何关键字直接编写一个变量赋值语句，而你所要赋值的变量根本没有定义，那么这个变量会被声明为全局变量。比如：

    ```javascript
    function fn() {
        foo = 100;
    }
    fn();
    console.log(foo); // 100
    ```

    这里函数体里的 `fn` 在赋值的时候没有定义，那么它就会被提升为隐式全局变量，即使在函数体外也可以访问。

    但是这个问题很快被解决了，只需要使用 JavaScript 的严格模式就可以禁止隐式全局变量。使用方式是直接在代码开头加入字符串 `"use strict"`。

    ```javascript
    "use strict";
    function fn() {
        foo = 100;
    }
    fn();
    console.log(foo); // Uncaught ReferenceError: foo is not defined
    ```

    ---

    事实上，读者可以发现上面演示的所有变量声明均是使用 `var` 声明。其实，在最初的 JavaScript 语言只有 `var` 关键字可以用于声明变量，并且严格按照变量提升的规则转换。然而由于变量提升规则很反直觉，并且函数作用域粒度过大，可能导致使用不便（参见**闭包**章节），所以新标准引入了 `let, const` 关键字，通过这两个关键字声明的变量遵循块作用域，并且不发生提升：

    ```javascript
    console.log(a); // Uncaught ReferenceError: a is not defined
    let a = 1;

    console.log(b); // undefined
    var b = 1;
    ```

    这也是我们强烈推荐只使用 `let, const` 关键字的原因。

### 箭头函数

一种更简洁的函数表达式的记号是这样的：

```javascript
// 以下几种写法的结果相同，更加细节的区别在下一节
const sum = (x, y) => x + y;
const sum = (x, y) => {
    return x + y;
}
function sum(x, y) {
    return x + y;
}
```

当函数体只有一个返回语句时，可以省略外层的括号和 `return` 关键字，若只有一个参数，可以省略参数列表的小括号：

```javascript
const plusOne = x => x + 1;
```

由于 JavaScript 中大量存在匿名函数，上述记号将帮助你少些很多 `function` 关键字，你将在下一部分意识到这一点。

另外值得注意的是，尽管在只有一个返回语句是可以省略括号和 `return`，但返回一个对象字面量时会引起错误的解释：

```javascript
// buggy
const objConstructor = (value) => { key: value };
```

这种写法会使 JavaScript 认为括号是代码段的开始，而其中的 `key: value` 是语法错误，通常我们添加小括号来告诉 TypeScript 我们要表达的是返回值字面量：

```javascript
const objConstructor = (value) => ({ key: value });
```

### 回调模式

!!! note "你 JavaScript 真正的第一步"

    回调模式可以认为是 JavaScript 的一大“灵魂”，这门语言的很多功能均基于这一设计模式。实际上，即使你可能没有听编者的话，偷偷看了不少 JavaScript 的魔法，深刻领悟一番之后觉得已经大彻大悟。但编者想要告诉你的是，那些魔法并不是 JavaScript 的核心，回调和到处飞的匿名函数才会是你实际接触 JavaScript 工程时处理最多的。

    这一部分将会讲解回调模式。如果读者已经理解掌握，则可以跳过这一部分。

回调模式从表面上讲，实际就是**将函数作为参数传入另一个函数**。

我们首先需要理解什么是函数参数。当一个函数无法自己获取到某一些信息的时候，就需要通过外部将这些信息通过参数通知给函数。比如说这样的代码：

```javascript
function sum(x, y) {
    return x + y;
}

// We tell the function that it needs to add 1 & 3 by passing parameters.
let a = sum(1, 3);
```

函数 `sum` 是没有办法自己获取其需要给哪两个数值求和，所以外部调用函数的时候需要传入这个信息。也就是说，函数参数的作用是补全函数所需要了解的信息。

但是有一些函数，它欠缺的不一定是“某一个变量是什么”这样的信息，它欠缺的可能是“我们需要它做什么”这样的信息。比如说我们想把上面的 `sum` 函数拓展功能，升级为一个加减乘除都能算的函数。那么问题就出现在，我如何告诉这个函数，它应该对 `x, y` 做什么操作。

一种解决方式是传入一个整数或者一个字符，表示这个函数应该进行什么样的运算：

```javascript
function calculate(x, y, op) {
    switch (op) {
        case "+": return x + y;
        case "-": return x - y;
        case "*": return x * y;
        case "/": return x / y;
        default: return undefined;
    }
}
```

但这种解决方法就需要我们事先约定 `op` 字符的值和函数进行的操作之间的关系，那为何我们不能直接告诉 `calculate` 函数它需要进行什么操作呢？

我们通过函数描述对数据的操作，而告诉函数它需要进行什么操作，就是**将描述操作的函数作为参数传入这个函数**，这个被传入到其他函数里的函数就称为**回调函数**。其名称可以大致理解为“这个函数现在传进去但暂且不调用，需要的时候再调用”：

```javascript
function calculate(x, y, op) {
    return op(x, y);
}

calculate(1, 2, function (x, y) { return x + y; }); // 3
calculate(1, 2, function (x, y) { return x - y; }); // -1
calculate(1, 2, function (x, y) { return x; }); // 1
```

关于回调函数更深层次的运用会在 JavaScript 异步部分讲解。

---

事实上，JavaScript 有关数组的各种操作几乎全都使用了回调模式，比如说遍历数组的操作：

```javascript
[1, 2, 3, 4].forEach(function (val, ind) {
    console.log(`The elem with index ${ind} is ${val}.\n`);
});
```

`forEach` 方法接受一个回调函数，并会把数组的每一项作为参数传入这个回调并执行。

另外的例子还有数组的映射函数 `map`：

```javascript
[1, 2, 3, 4].map(function (val) {
    return 2 * val;
}); // [2, 4, 6, 8]

let arr = [1, 2, 3, 4];
let newArr = [];
for (let i = 0; i < arr.length; ++i) newArr.push(2 * arr[i]);
```

这两种写法是等价的，但是 JavaScript 更倾向于选择前者，因为这种回调式的链条可以很方便地加长，在处理数组的时候尤其方便。比如说我们需要将接收到的数据清洗格式之后筛选出合法数据并排序：

```javascript
response.data
    .map(function (val, ind) {
        return {
            ...val,
            index: ind,
        };
    })
    .filter(function (val) {
        return val.name !== undefined;
    })
    .sort(function (a, b) {
        return a.subscriberNum - b.subscriberNum;
    });
```

如果用 C/C++ 格式进行编写，则可能稍显复杂。

!!! note "展开运算符"

    这里我们使用了 `...` 运算符，该运算符如果讲解其运作细节则显得冗余，所以这里讲解其在实际工程中常见的用法：

    ```javascript
    let obj = { a: 1, b: 2, c: 3 };
    let another = { ...obj, c: 2, d: 4 }; // { a: 1, b: 2, c: 2, d: 4 }
    ```

    这里 `...obj` 的含义就是将 `obj` 对象展开成键值对，也就是说这里 `another` 对象就获得了 `a: 1`、`b: 2`、`c: 3` 这三个键值对。而如果后续的键值对和 `obj` 之中的发生重合（如这里 `another` 中重新定义了 `c` 这个属性），则后者覆盖前者。所以这里新定义的 `c: 2` 覆盖掉 `obj` 之中的 `c: 3`。

    这个语法的重要用途就是，如果我需要根据一个已有对象 `obj` 构造一个新对象 `another`，新对象和已有对象仅有一个键值对不同，就可以用这个语法：

    ```javascript
    const getNewObjectWithOnePropChanged = (obj, value) => {
        return {
            ...obj,
            keyToBeChanged: value,
        }
    }
    ```

### 延迟执行

在各个 JavaScript 运行时中都包含了以下两个函数用于定时执行任务：

```javascript
setTimeout(() => {
  // do something
}, 1000);

setInterval(() => {
  // do something
}, 1000);
```

两个函数传入的第一个参数都是要执行的任务，第二个函数是等待的时间（毫秒数）。不同之处在于 `setTimeout` 的回调只执行一次，`setInterval` 的回调函数会以传入的时间间隔反复执行。

需要注意的是，由于 JavaScript 的事件循环模型，上述的延迟时间并不是精确的，例如当其他复杂任务正在阻塞运行时，任务会按照上述时间添加到队列中，但执行时间会有所延迟。

### 闭包

函数既然是一个对象，那么我们也可以用一个函数返回一个函数。比如说：

```javascript
function getIncreaser(increment) {
    return function (x) {
        return x + increment;
    }
}

const increase5 = getIncreaser(5);
increase5(10); // 15
```

比如说上述的 `getIncreaser` 可以用于获取一个“数据增长器”，而具体给数据增加多少，则是这个函数接收的参数。`getIncreaser` 会把 `increment` 变量包装在其返回的匿名函数之中，这样就形成了一个**闭包**。

闭包实际上就是一个函数和对其**周围状态（lexical environment，词法环境）的引用**捆绑在一起。这里要特别注意的是，闭包的所谓词法环境，指的是创建闭包时的**函数作用域**。

但闭包会带来一定的问题，比如说这样的代码：

```javascript
function getCounters() {
    var arr = [];
    for (var i = 0; i < 3; ++i) {
        arr.push(function () { console.log(i * i); });
    }
    return arr;
}

var counters = getCounters();
counters[0](); // 9
counters[1](); // 9
counters[2](); // 9
```

这些代码的预期执行结果应该是 `0, 1, 4`，因为在将函数打包的时候 `i` 的值应该分别是 `0, 1, 2`。但是要注意，函数打包成闭包的时候，只单纯存储了**临时变量的引用**，而到真正执行闭包函数的时候才会去访问这些临时变量的值。上面例子之中的闭包函数在真正执行的时候，所引用的 `i` 已经是 `3` 了，所以结果全为 `9`。

!!! caution "闭包引用会发生变化的量是危险的"

    根据上面的例子，我们就已经可以看出，闭包所引用的临时变量最好是常量或确定不会发生变化的量，这样至少可以让闭包的行为可控。

我们来想想怎么解决这个问题，读者可能已经想到了一种解决方式，也就是把 `i` 包装到闭包之前首先使用其它变量捕获：

```javascript
function getCounters() {
    var arr = [];
    for (var i = 0; i < 3; ++i) {
        var j = i; // Capture i here
        arr.push(function () { console.log(j * j); });
    }
    return arr;
}

var counters = getCounters();
counters[0](); // 4
counters[1](); // 4
counters[2](); // 4
```

但依然有 bug，原因是 JavaScript 的变量提升，这一部分可以参考先前的讲解。由于 `j` 使用了 `var` 声明，所以其声明实际上会被提升到函数最前面，导致其还是跟随着 `i` 被修改了。

为了防止变量提升，有一种解决方式再创建一个函数作用域防止过度提升：

```javascript
function getCounters() {
    var arr = [];
    for (var i = 0; i < 3; ++i) {
        arr.push((function (i) {
            return function () { console.log(i * i); };
        })(i));
    }
    return arr;
}

var counters = getCounters();
counters[0](); // 0
counters[1](); // 1
counters[2](); // 4
```

这里我们又创建了一个闭包，只不过这个闭包立刻执行，把 `i` 传进去，这样的话 `i` 就变成了这个闭包内部的参数 `i`，不会随着外面的循环变量 `i` 改变而改变。

但实际上之前讲解变量提升的时候我们就提过，解决变量提升最好的方式就是不用 `var`。所以说解决这个问题的最好方案应该是：

```javascript
function getCounters() {
    let arr = [];
    for (let i = 0; i < 3; ++i) {
        arr.push(function () { console.log(i * i); });
    }
    return arr;
}

let counters = getCounters();
counters[0](); // 0
counters[1](); // 1
counters[2](); // 4
```

这样的代码不仅正确了，可读性也很好。

!!! note "为什么是对的？"

    事实上这里正确并不是因为 `let` 关键字带来了什么特殊处理，而是因为作用域不再是函数作用域。
    考虑下面的代码，虽然使用了 `let` 关键字，但作用域仍是函数作用域：

    ```javascript
    function getCounters() {
        let arr = [];
        let i = 0;  // 注意在这里声明的 i 是函数作用域
        for (i = 0; i < 3; ++i) {
            arr.push(function () { console.log(i * i); });
        }
        return arr;
    }

    let counters = getCounters();
    counters[0](); // 9
    counters[1](); // 9
    counters[2](); // 9
    ```

    可以看到，结果仍然是错误的。

    那么为什么作用域是局部作用域时就对了呢？
    原因在于在闭包捕获变量时，对局部作用域的变量会**复制**，而对函数作用域的变量会**引用**。
    因此，通过在 `for` 语句中使用 `let` 声明变量，我们声明了局部变量，从而获得了期望的复制捕获行为。

此外需要注意的是，JavaScript 在任何情况下都不会帮你复制**对象**（上面被捕获的 `int` 是基本值而非对象），如果你的闭包捕获一个对象的值，无论这个对象的作用域如何，都会产生类似问题。事实上，这里并非基本值被特殊地对待，而是对象类型的变量存储的值是到实际对象的引用，因此在捕获时复制的是引用（浅拷贝）而非实际对象（深拷贝）。除非你明确地想要保存一些值，否则在构造闭包时请注意捕获实际的值，而非引用。
