# JavaScript 语言基础

## 前置知识

- 具有 C/C++ 语言基础，能够编写基本的 C/C++ 代码

- 具有面向对象程序设计的基础，能够理解类封装和方法重写（语言最好是 C++）

- （如果需要学习 JavaScript 异步）能够理解基本的多线程和异步，理解异步和同步的差别

- （如果需要学习 JavaScript 异步）能够理解栈和队列两种数据结构，理解函数栈的工作机制

!!! note "边学边做"

    阅读这篇文档的时候，编者建议按下 F12 键，打开浏览器的控制台，把编者提供的示例代码在控制台里真正运行一下，相信会给帮助你更好理解 JavaScript 语言的。

## JavaScript 的变量与变量类型

### 变量声明

JavaScript 语言之中声明变量的关键字有三个，分别为 `var, let, const`。从字面意义上来看，`const` 用于声明常量，而 `var, let` 则用于声明变量。在 `var, let` 选取的问题上，编者建议尽量全部使用 `let` 关键字声明变量以防止混乱，具体原因可以参照讲解 JavaScript 函数部分的**变量提升**注解。

```javascript
var a = 0;
a = 1; // OK
let b = "Test string";
b = "New string"; // OK
const c = 0;
c = 2; // Error!
```

另外，由于 JavaScript 是弱类型的，所以重新给变量赋值的时候可以改变类型：

```javascript
let weakType = 0;
weakType = "You are a string now!"; // OK
```

### 变量类型

!!! caution "JavaScript 的魔法"
    
    JavaScript 是一个在两周左右时间就设计完成的语言，即使这门语言的设计者水平很高，但不可否认的是如此紧迫的工期也造成了 JavaScript 之中有许多反直觉的设计缺陷，这些缺陷编者会在文档各对应部分进行讲解。

    JavaScript 的基础类型和基础运算符的表现在大多数情况下和 C/C++ 语言类似，但是由于 JavaScript 的语言特性或缺陷，这些基础运算符在另外一些常识之外的运算之中有着相当**超凡脱俗的**和反直觉的表现。你可以认为这是 JavaScript 的设计缺陷，也可以认为是这门语言的语法特性。

    其中，已经被作为语法特性而被广泛运用的用法将会在正文中指出，而其他用法将会在这一节的最后具体介绍。如果你是 JavaScript 语言的初学者，编者并不建议阅读这一部分，因为这可能会造成不良好的编码习惯和编程思维的混乱。就初学而言，阅读本文档的正文就足以写出实用且强大的代码。但如果你想要深入了解这一门语言，编者认为理解这些运算符的具体表现是有必要的。

JavaScript 语言支持五种基本类型，即数字、字符串、布尔值、`undefined` 类型和 `null` 类型。前三个类型是容易理解的，而后两者则会在最后讲解。

!!! note "一些适当的简化"

    实际上最新的 JavaScript 具有七种基本类型，多出的两种为 `Symbol` 类型和大整数类型。但由于这两者在实际工程之中使用较少，所以本文档作为入门文档并不会介绍，本文档默认 JavaScript 只有五种基本类型。

    如果需要学习这两个拓展类型，可以参考文档末给出的链接。

获取数据的类型可以使用 `typeof` 关键字：

```javascript
let testNum = 0;
typeof testNum; // "number"
```

#### 布尔、数字和字符串

JavaScript 的布尔值类型仅有两个可能值，即 `true` 和 `false`。与布尔值类型相关的运算一般而言和 C/C++ 语言类似：

```javascript
4 > 5; // false
2 !== 3; // true
"I" === "You"; // false

false && false; // false
true || false; // true
!false; // true
```

需要注意的是，JavaScript 语言的判等运算符有 `==, ===` 两种，判不等运算符也相应地有 `!=, !==` 两种。编者建议完全使用 `===, !==` 来代替 `==, !=`，这是因为后者在比较之前可能会发生强制类型转换，而这种转换通常不必要且难以理解。另一方面，前者则会进行严格的值比较，不进行类型转换。

```javascript
1 == true; // true
1 === true; // false
```

JavaScript 语言之中的数字不区分整数和浮点数，统一使用浮点数表示。所以除法没有类似 C/C++ 的向下取整的性质：

```javascript
5 / 2; // 2.5
```

部分数学相关的函数、常数均可以使用内置对象 `Math` 调用，不过涉及到精确的数学计算的时候一定要注意浮点数误差问题：

```javascript
Math.PI; // 3.141592653589793
Math.floor(5 / 2); // 2

Math.sin(Math.PI); // 1.2246467991473532e-16
0.1 + 0.2; // 0.30000000000000004
```

JavaScript 语言的数字类型还有两个保留字，即 `NaN` 和 `Infinity`，这两者分别代表“不是数字”和“无穷大”。`NaN` 通常是不合法运算的结果，而且 `NaN` 参与的算术运算只会得到 `NaN`：

```javascript
1 / "tql"; // NaN
Math.sqrt(-1); // NaN
parseInt("Not a number"); // NaN

1 + NaN; // NaN
NaN + NaN; // NaN
```

`NaN` 还需要注意一点，就是 `NaN` 和比较运算符的连用：

```javascript
NaN < 1; // false
NaN > 1; // false
NaN === NaN; // false
NaN !== NaN; // true
```

这就代表我们在判定一个值是否是 `NaN` 的时候**不能**使用运算符 `===`，而应当使用 `isNaN` 函数。这个函数**首先会将参数强制转化为数字类型**，并且只在转化结果为 `NaN` 的时候返回 `true`：

```javascript
isNaN(NaN); // true
isNaN(3); // false

isNaN("2.4"); // false, "2.4" can be converted to 2.4
isNaN("No"); // true, "No" can never be converted to legal number

isNaN(true); // false, true can be converted to 1
```

!!! note "强制类型转换"

    JavaScript 进行强制类型转换的逻辑会写在本节末尾，但我们依然建议初学者先阅读正文，学会编写安全的代码后再去深入理解。

我们在代码之中很少直接写出 `NaN` 字面量，但是我们依然需要注意各种运算可能产生的 `NaN`，并且合理使用 `isNaN` 函数进行分支判定，以防止程序出现 bug。

`Infinity` 代表无穷大，如果运算结果超出了 JavaScript 能处理的范围，则会得到 `Infinity`。`Infinity` 所参与的算术运算也一般符合数学直觉，如果涉及到不定式（零乘以无穷大、无穷大减无穷大等）则会得到 `NaN`：

```javascript
1 / 0; // Infinity
-1 / 0; // -Infinity
1e100000; // Infinity
0 / 0; // NaN

Infinity + 1; // Infinity
Infinity - 1; // Infinity
Infinity * 2; // Infinity
Infinity / 3; // Infinity

Infinity * 0; // NaN
Infinity - Infinity; // NaN

Infinity === Infinity; // true
```

JavaScript 的字符串支持使用加法运算符拼接，同时也支持相当多的常用函数。这里展示一部分：

```javascript
"a" + "b"; // "ab"
"hello".charAt(0); // "h"
"hello, Mike".replace("Mike", "Mart"); // "hello, Mart"
"hello, Mike".indexOf("Mike"); // 7
"hello".substring(2, 3); // "l"
```

这里需要介绍的是**模板字符串**，这种字符串不使用单引号或双引号包围，而是使用反引号包围，内部可以使用 `${}` 块包围代码块，JavaScript 会计算出代码块的结果并将其转化为字符串嵌入模板之中。这个语法的好处在于不需要手写很多 `+` 来手动拼接字符串：

```javascript
let i = 1;
`The val of i + 1 is ${i + 1}.`; // "The val of i + 1 is 2."
```

另外注意一点，JavaScript 允许任意变量和字符串相加。而最常用的是字符串在加号左侧，其他变量在加号右侧的形式，这种运算的逻辑是将其他变量转化为字符串后进行字符串拼接。这就诞生了一个 trick，即用一个空字符串加一个变量，就可以方便地将这个变量转化为字符串：

```javascript
"4" + 3; // "43"

"" + 3; // "3"
"" + true; // "true"
```

而将字符串转换为数字则可以使用 `parseInt` 和 `parseFloat` 函数，这里讲解 `parseInt` 函数。这个函数接受两个参数，第一个是需要转换的字符串，第二个是转换的进制数。不传入这个参数的时候默认根据字符串格式确定，如果以 `0x` 开头，则按照十六进制转换，其他则按照十进制转换：

```javascript
parseInt("132"); // 132
parseInt("0x10"); // 16
parseInt("010"); // 10

parseInt("365", 10); // 365
parseInt("10", 4); // 4
```

!!! caution "八进制问题"

    在老版本的 `parseInt` 之中，在不传入第二个参数的时候，以 `0` 开头的字符串会被按照八进制转换：

    ```javascript
    parseInt("010"); // In old version: 8
    ```

    为了避免这种适配问题出现，建议使用这个函数的时候不使用其默认进制，而是手动通过第二个参数指定。

`parseInt` 函数在转换失败的时候会返回 `NaN`。但要注意的是，这个函数的逻辑是逐个读取字符并实时转换，遇到不能转换的字符的时候返回已经转换好的结果而非 `NaN`：

```javascript
parseInt("hello", 10); // NaN
parseInt("123abc", 10); // 123
```

#### 对象和数组

对象类型是 JavaScript 语言之中最常用的复合类型，其由若干的键值对组成，每一个键值对之中值可以是任何类型的变量，同时也允许对象的嵌套。对象字面量使用花括号表示，花括号内部键值对使用逗号分隔，每一个键值对的键和值使用冒号分隔：

```javascript
let obj = {
    foo: 0,
    bar: "bar",
    foobar: {
        a: 1,
        b: "I am a value",
    },
};
```

每一个键值对之中的键称为这个对象的**属性**。访问给定对象的属性使用 `.` 运算符：

```javascript
let obj = { foo: 1, };
obj.foo; // 1
```

JavaScript 的数组使用中括号，各元素之间使用逗号分隔，且 JavaScript 不要求数组元素都是同一类型。访问数组的某个元素使用 `[]` 运算符：

```javascript
let arr = [1, false, "test", { foo: 1, }];
arr[2]; // "test"
```

JavaScript 数组有内置属性 `length`，其值为可用的最大索引数加一：

```javascript
let arr = [1];
arr[100] = 2;
arr.length; // 101
```

JavaScript 数组的真正强大之处在于其内置的方法，除去常用的取索引、切片方法之外，还支持传入回调函数的遍历、映射、迭代、筛选、排序方法。这里先介绍前面的简单方法，涉及到回调函数方法则会在回调模式讲解完毕后讲解：

```javascript
let arr = [1, 2, 3];
arr.indexOf(1); // 0
arr.indexOf(4); // -1
arr.slice(1, 2); // [2]
arr.join("."); // "1.2.3"
```

!!! caution "数据安全"

    使用数组内置方法的时候，一定要注意**这个方法是否会影响原数组**。比如说 `slice` 方法获取的是一个全新的数组，在这个新数组上操作不会影响原有数组。而 `pop` 方法则会影响原有数组。

    ```javascript
    let arr = [1, 2, 3];
    let slicedArr = arr.slice(1, 3);
    slicedArr[0] = 1;
    console.log(arr); // [1, 2, 3]

    arr.pop();
    console.log(arr); // [1, 2]
    ```

    如果有些方法会影响到原有数组而你并不想这样做，请一定要在调用方法前创建副本以防止数据丢失。此外，为了养成良好编码习惯，在使用语言内置的方法，乃至第三方库的方法的时候，一定**仔细阅读文档**以确定这些方法的行为符合预期且可控。

#### 空类型

`undefined` 类型和 `null` 类型被称为空类型。两者的区别可以直观理解为“空的程度不一样”，`null` 是空的，但它是存在的，而 `undefined` 不存在，它没有被定义。

事实上，在实际的 JavaScript 编程之中，我们更容易遇到 `undefined`。以下情况均会得到 `undefined`：

- 访问没有赋值的变量

- 数组越界访问

- 访问对象上不存在的属性

- 获取没有返回值的函数的返回值

```javascript
let x;
let arr = [1, 2];
let obj = { foo: 1, };
function foo() { }

x; // undefined
arr[5]; // undefined
obj.bar; // undefined
foo(); // undefined
```

在 `undefined` 上访问属性会抛出错误：

```javascript
undefined.anyProp; // Uncaught TypeError: Cannot read properties of undefined
```

这个错误会在 JavaScript 编程的时候经常遇到，而且原因常常是类似如下语句的连续属性访问：

```javascript
response.data.propList[3].name;
```

只要这里某一个属性访问访问到了不存在的属性或者数组越界，那么下一个属性访问将会访问 `undefined` 上的属性，导致程序报错退出。

而一种解决方式是每进行一次属性访问就判定其是否获得了 `undefined`，如果是，则终止继续的属性访问。而较新的 JavaScript 语言规范之中引入了 `?.` 运算符，其作用是**在这个属性访问出错的时候中断属性访问并返回 `undefined`**。

比如上述代码之中，如果你已经知道 `response` 很有可能没有 `data` 属性，那么可以这样写：

```javascript
response.data?.propList[3].name;
```

如果 `response` 确实没有 `data` 属性，那么整个式子的结果就是 `undefined`，但程序并不会报错退出。

!!! note "问号应该在哪里"

    答案是，`?.` 应当出现在可能导致访问 `undefined` 的属性访问上，而非可能导致访问结果为 `undefined` 的属性访问上。

    比如说不确定 `response` 是否有 `data` 属性的时候，就是不确定 `response.data` 是否为 `undefined`，那么就应该在后续的访问使用 `?.` 代替 `.`。

    如果写成：

    ```javascript
    response?.data.propList[3].name;
    ```

    实际上在 `response` 确实没有 `data` 属性的时候程序还是会因为访问 `undefined` 的属性而报错。

你甚至可以这样尝试 `?.` 运算符：

```javascript
undefined?.anyProp; // undefined
```

这一次程序不会报错。

---

而 `null` 相较于 `undefined` 则较为少见。其一个常见的应用场景是在 React 框架里表示这里不需要渲染：

```javascript
<div>
    <p> Hello! </p>
    {
        needToRender ? <p> This part is necessary! </p> : null
    }
</div>
```

这一部分可以参考 [React 入门文档](/frontend/react-easy)。

而直接访问 `null` 上的属性也会报错。同时，在处理这个问题的时候 `?.` 依然可以使用：

```javascript
null.anyProp; // Uncaught TypeError: Cannot read properties of null
null?.anyProp; // undefined
```

??? note "一些和空类型相关的语法糖"

    在实际的 JavaScript 工程之中，我们常常会遇到类似这样的需求：

    > 将某一个变量设定为某一个值，当给定值未定义的时候，则设定为某一个默认值。

    比如说要将字符串 `x` 设定为字符串 `y` 的值，但在 `y` 未定义或者为空字符串的时候 `x` 设定为默认值 `"default"`。一般而言可以写为：

    ```javascript
    if (y === undefined || y === "") x = "default";
    else x = y;
    ```

    但实际上 JavaScript 的逻辑运算有一个类似语法糖的写法可以完成这一功能：

    ```javascript
    x = y || "default";
    ```

    !!! caution "注意不等价转换"

        实际上这种语法糖写法和之前的 `if-else` 写法**并不完全等价**，具体原因可以参阅 JavaScript 的魔法指导书。

        这种写法要求 `y` 一定是一个字符串，但考虑到大多数工程场景下 `y` 的类型都能得到保证，故还是可以在合适的条件下采用这种写法。

    另外，有一个类似的运算符为 `??`。与 `x = y ?? "default"` 等价的代码块为：

    ```javascript
    if (y === undefined || y === null) x = "default";
    else x = y;
    ```

    此外，还有一种常见的需求：

    > 检查某个函数是否定义，如果定义，则执行。

    这个需求可以这样实现：

    ```javascript
    if (foo !== undefined) foo();
    ```

    而 JavaScript 所提供的一种语法糖写法是：

    ```javascript
    foo && foo();
    ```

    但和上述一样，这个转换并非完全等价，但考虑工程实际，一般而言是可以采用这种写法的。

??? note "空的三个境界"

    除去 `undefined` 和 `null`，实际上空对象 `{}` 也是空的一种。但是这三个空的境界各有不同。

    如果用盒子比喻 JavaScript 对象，将键值对比作盒子中的物品。那么 `{}` 是空盒子，`null` 是连盒子都没有，而 `undefined` 是大家都不知道盒子是什么。

    但实际工程中，`null` 和 `undefined` 的差距很小，除去很细微的行为差异外（比如两者强制类型转换为数字的时候结果并不一样），两者几乎等同。而 `{}` 则和这两者完全不同，我们可以访问它的属性（虽然返回 `undefined`），可以对它做更多的运算。它只是一个空盒子，我们可以往里面放东西让它从无到有。但 `null, undefined` 则是干干净净的空，几乎无法利用。

    那为什么 JavaScript 的 `null` 和 `undefined` 的差距很小，但设计者依然要同时设定这两者呢？这个问题并没有确切的解答。

??? caution "JavaScript 的魔法指导书"

    现在我们来讲述 JavaScript 的基础运算符的一些反直觉表现，以及这些行为所带来的一些魔法。我们并不希望初学者阅读这一部分，以防形成不良好的编码习惯。

    首先关注四大基础算术运算符 `+, -, *, /`。这四个运算符的基本逻辑类似，大致为：

    - 首先将两边参与运算的变量转化为五大原始类型
    - 转换完毕后如果有某一个变量为字符串且运算为加法，则将另外一个强制转换为字符串进行字符串拼接运算
    - 其他情况则是将两者均强制转换为数字进行算术运算

    那么我们首先关注**强制转化为五大原始类型**的逻辑：

    - 如果转化变量就是五大原始类型，则返回其本身
    - 如果转化变量不是五大原始类型，则判断其是否为日期 `Date` 类型
        - 如果为 `Date` 类型，则调用其 `toString()` 方法转化为字符串
        - 如果不为 `Date` 类型，则调用其 `valueOf()` 方法，其返回值参与运算。如没有该方法或其返回值不是五大基本类型，则调用其 `toString()` 方法，其返回值参与运算。如果还没有该方法或其返回值不是五大基本类型，抛出 `TypeError` 错误

    而四大基本类型强制转换为数字的逻辑如下：

    - `undefined` 强制转换为 `NaN`，`null` 强制转换为 `0`
    - `true` 强制转换为 `1`，`false` 强制转换为 `0`
    - 如果字符串是合法的十进制浮点数，则转化为浮点数。如果字符串是合法的十六进制数，则按照十六进制规则转化。如果字符串只包含数字和开头的负号，则按照十进制规则转化。如果是空字符串，则转化为 `0`。如果字符串为 `"Infinity"` 或者 `"-Infinity"` 则分别转化为 `Infinity` 和 `-Infinity`。其余情况转化为 `NaN`

    四大基本类型强制转换为字符串的逻辑如下：

    - `undefined` 强制转换为 `"undefined"`，`null` 强制转换为 `"null"`
    - `true` 强制转换为 `"true"`，`false` 强制转换为 `"false"`
    - 数字强制转化为十进制字符串形式的数字，`NaN` 强制转换为 `"NaN"`，`Infinity` 强制转换为 `"Infinity"`，`-Infinity` 强制转换为 `"-Infinity"`

    另外还要说明的是，JavaScript 之中的 `+` 运算符也是一种一元运算符，其作用是将操作变量强制转换为数字类型。而这种强制转换的逻辑也是先把操作变量强制转换为基本类型后再强制转换为数字：

    ```javascript
    +"123"; // 123
    +[]; // 0
    ```

    这里对空数组做的强制转换的逻辑为，首先调用 `[].valueOf()`，结果为 `[]`，不是基本类型。进一步调用 `[].toString()`，结果为 `""`，是基本类型。从而再将 `""` 强制转换为数字得到 `0`。

    知晓这种运算逻辑之后，我们来看看一些魔法运算，读者可以尝试向上面一样分析这些运算的内部哲学：

    ```javascript
    [] + []; // ""
    [] + {}; // "[object Object]"
    +[] + (+[]); // 0

    1 + null; // 1
    1 + undefined; // NaN

    1 + new Date(); // "1Mon Oct 04 2021 02:04:31 GMT+0800 (中国标准时间)"
    +new Date(); // 1633284276012

    "90" + 1; // "901"
    "90" - 1; // 89

    "Infinity" + "-Infinity"; // "Infinity-Infinity"
    "Infinity" - "Infinity"; // NaN

    "8" * 5; // 40
    4 / "5"; // 0.8
    ```

    不过有一点我们需要注意，也就是空对象 `{}` 在不同的平台上的解析是不同的。比如说算式 `{} + {}` 中的第一个空对象，有些浏览器会解析为空对象，而有些会解析为空代码块。为了防止混淆，建议加一个小括号写成 `({}) + ({})`。

    ---

    下面我们研究三个逻辑运算符 `&&, ||, !`。

    `&&` 的逻辑为：

    - 首先运算左侧的表达式，并将结果强制转化为布尔值，如果为 `false`，则短路（不计算右侧表达式），返回左侧表达式的值（返回原值，不强制转化为布尔值）
    - 否则计算右侧的表达式，并将结果直接返回

    `||` 的逻辑类似：

    - 首先运算左侧的表达式，并将结果强制转化为布尔值，如果为 `true`，则短路（不计算右侧表达式），返回左侧表达式的值（返回原值，不强制转化为布尔值）
    - 否则计算右侧的表达式，并将结果直接返回

    而各种变量转化为布尔值的逻辑如下：

    - `undefined, null, 0, NaN, "", false` 转化为 `false`
    - 其余均转化为 `true`（无论是对象还是基本类型）

    这个逻辑实际上造就了一个相当诡异的运算规则。JavaScript 的布尔值类型是有一个包装类 `Boolean` 的，那么我们可以创建这样的一个对象 `Boolean(false)`，由于其是对象，其强制转换为布尔值的时候会得到 `true` 而非直觉的 `false`。

    此外，`!` 运算符的逻辑则很简单，首先将操作数强制转化为布尔值类型，之后取反作为返回值。

    也就是说，实际上这些逻辑运算符，只有 `!` 一定返回布尔值类型。甚至可以说 `&&, ||` 很多时候不返回布尔值类型。

    那么就可以观察这些运算：

    ```javascript
    ![]; // false
    [] && 0; // 0
    0 || {}; // {}

    !(new Boolean(false)); // false
    ```

    <!-- TODO: JavaScript 比较运算符的魔法 -->

    综合这些运算，我们来看看下面的混合运算：

    ```javascript
    !+[]+[]+![]; // "truefalse"
    ```

    是不是就显得很合理了？

    ---

    实际上现在已经有一个把 JavaScript 这些特性玩出新花样的代码库了，即 JSFuck，其宗旨为只用 `+[]!()` 六个字符编写可运行的 JavaScript 代码。可以说 JSFuck 代码完全依靠 JavaScript 的这些 bug 在工作。

## JavaScript 的控制语句

JavaScript 支持的控制语句关键字包括 `if, else, switch, case, while, do, for`，其用法和 C/C++ 语言几乎完全一致。这里给出样例程序不进行具体的讲解：

```javascript
let i = 0;
if (i > 0) {
    console.log("i is greater than 0");
} else {
    console.log("i is not greater than 0");
}

let arr = [1, 2, 3, 4, 5];
for (let j = 0; j < arr.length; ++j) {
    console.log(`arr[${j}] = ${arr[j]}\n`);
}
```

不过需要注意的是，涉及到条件判断的时候会发生**强制类型转换**（将变量强制转换为布尔值），其逻辑为：

- `undefined, null, 0, NaN, "", false` 转化为 `false`
- 其余均转化为 `true`（无论是对象还是基本类型）

值得注意的是布尔值类型的包装类 `Boolean` 对象，无论其表示 `true` 还是 `false`，其均会被转化为 `true`，所以需要特别注意类似下述程序的情况：

```javascript
if (new Boolean(false)) {
    console.log("WTF?");
} // "WTF!"
```

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

比如说上述的 `getIncreaser` 可以用于获取一个“数据增长器”，而具体给数据增加多少，则是这个函数接收的参数。`getIncreaser` 会把 `increment` 变量包装在其返回的匿名函数之中，这样就形成了一个**闭包**。闭包实际上就是一个包装了其所在环境的一些局部变量的函数。

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

## JavaScript 的异步

!!! note "异步的必要性"

    正常我们编写的代码都是同步代码，也就是说代码执行的顺序是从上到下，上面的操作还没有执行完，下面的操作就不会被执行到。但是在前端代码之中，我们会经常向后端或者网络请求数据，即使现在网络已经很快，在请求数据量较大的时候，请求数据的操作依然会占据相当长的时间。如果我们还依然使用同步代码，那么就会导致页面卡顿（因为这个时候代码卡顿在了请求数据这一步）。

    另外一方面，有些操作完全可以在前端请求页面的时候完成，比如说渲染出页面的模板。我们可以在数据完全获取之后再把这些数据填入页面。

    总而言之，前端需要使用异步代码，需要允许多段代码同时执行而非严格按照顺序一步步走。这种允许多段代码同时执行的代码就是**异步（英语：Asynchronous）**。

### 事件循环与消息队列机制

首先我们需要明确，JavaScript 是单线程语言，这也就代表 JavaScript 并不能像 C++ 或者 Java 那样实现多线程并发的异步。

!!! note "为何禁用多线程"

    JavaScript 的设计初衷就是浏览器的脚本语言，其作用就是修改文档树。如果 JavaScript 允许多线程，那么其很有可能因为多线程并发修改文档树导致冲突。

但 JavaScript 使用了另外一种方式实现异步，也就是事件循环和消息队列机制。

在详细叙述这个机制之前，我们首先需要重新认识一下回调函数。当主线程派遣出一个异步过程，比如说触发了网络请求，一般而言需要设定一个这个异步过程的回调函数。这个回调函数描述的，是这个异步过程执行完毕后需要做的事情。

需要定义这个回调函数的原因也很简单，主线程没有办法了解到异步过程何时才能结束，甚至并不清楚这个过程能否结束。在这样的情况下，主线程完全不能知晓什么时候做这个异步过程的善后工作（比如说从网络获取数据后，主线程应该把这些数据加载到页面上）。那么不如定义一个回调函数，具体什么时候调用，根据异步过程来确定。

回到事件循环和消息队列。JavaScript 的主线程上会有一个死循环，称为**事件循环**。其执行逻辑为在空闲的时候不断检查消息队列是否有消息，如果有，则执行，如果没有，则等待。而空闲的含义是当前函数栈空。

而需要异步执行的代码则会负载一个回调函数，当异步过程执行完毕后，则会把回调函数放入消息队列末尾。当主线程空闲且检测到消息队列之中有回调函数的时候，主线程就会执行回调函数，做好异步过程的善后工作。

通过这样的机制，JavaScript 依然是单线程的，对文档树的操作依然是同步的、不会出现冲突的。但依靠这个机制，JavaScript 实现了异步。

!!! note "宏消息队列和微消息队列"

    实际上 JavaScript 的消息队列有两种，分为宏消息队列和微消息队列。`setTimeout, setInterval` 这类引发的回调会放入宏消息队列，而 `Promise` 的 `then` 会放入微消息队列。

    JavaScript 的事件循环在查看消息队列的时候，会首先查看微消息队列，如果存在回调则**执行每一个回调直到微消息队列清空**。之后再去查看宏消息队列，并且**只执行队列第一个回调**。

现在我们编写一个异步的代码。这里会用到 `setTimeout` 函数，其接受两个参数，第一个参数为一个回调函数，第二个参数为多长时间后执行上述回调函数。比如下列代码：

```javascript
setTimeout(() => {
    console.log("1s has passed!");
}, 1000);
```

这个代码将会延迟一秒后输出 `"1s has passed!"`。这个函数的等待过程会异步于主线程执行，而其通过第一个参数接受的回调就会在等待完毕后放入消息队列。现在我们利用这个函数写出一个需要耗时的操作，以此模拟网络请求：

```javascript
const fetchData = () => {
    setTimeout(() => {
        console.log("Data got!");
    }, 1000);
};
```

这个函数调用后会立刻返回，因为它的任务是派遣一个异步过程，而不负责等待异步结束和善后，善后工作应该写在回调函数之中等待主线程执行。

在等待异步的过程中，主线程可以完成其他的任务：

```javascript
const fetchData = () => {
    setTimeout(() => {
        console.log("Data get!");
    }, 1000);
};

fetchData(); // Dispatch async task
console.log("Rendering template...");
console.log("Loading local storage..."); // Main thread doing other tasks
```

### 回调函数的缺陷

回调函数不需要引入很多的其他语法就可以方便地使用到异步之中，但是其问题也是很突出的。回调函数本身可读性就不是很好，而且也并不能很好贴合我们的直观思维逻辑。而其最大的问题就是可能造成回调函数过分嵌套，导致代码难以维护。这一般被称为**回调地狱**。

真正的异步业务逻辑可能并不会很单一，比如说前端要从多个数据源加载数据，但是后一个数据的加载需要依靠前一个数据的结果。这样就不能在主线程中同时派遣多个异步过程，而必须在前一个异步过程的回调之中派遣下一个异步过程。这里我们依然用 `setTimeout` 来模拟耗时操作：

```javascript
setTimeout(() => { // Get data #1
    console.log("Data #1 get!");
    setTimeout(() => { // Get data #2
        console.log("Data #2 get!");
        setTimeout(() => { // Get data #3
            console.log("Data #3 get!");
            setTimeout(() => { // Get data #4
                console.log("Data #4 get!");
            }, 1000);
        }, 1000);
    }, 1000);
}, 1000);
```

这样的代码很难阅读，也很难维护，而实际上的业务逻辑也不可能是 `setTimeout` 这样简单。另一方面，我们还需要处理异步异常的情况，如果发生错误，就应当立刻跳出异步，交由主线程做异常处理。而显然，我们很难给这样的代码添加异常处理功能。

### `Promise` 对象

为了解决回调函数不直观的问题，新版本的 JavaScript 设立了 `Promise` 对象，可以说这个对象完全改变了 JavaScript 异步代码编写的规范。

创建一个 `Promise` 对象可以使用其构造函数：

```javascript
new Promise((resolve, reject) => {
    let asyncSucceeded = Math.random() > 0.2;
    setTimeout(() => {
        if (asyncSucceeded) {
            resolve("Async succeeded!");
        } else {
            reject("Async failed!");
        }
    }, 1000);
});
```

其构造函数接受一个回调函数，这个回调函数的两个参数是另外的两个回调函数。`resolve` 代表异步成功的回调函数，`reject` 代表异步失败的回调函数。而构造函数接受的回调函数的函数体则是需要执行的耗时操作。

!!! note "为什么要叫这个名字，它的构造函数为什么长成这样"

    Promise 的中文一般是“承诺”，那异步和承诺究竟有什么关系？

    我们来设想这样的一个情景。小明是一位五年级的小学生，他的妈妈为了让他好好学习，**承诺**小明如果他期末考试考到 100 分，就给他买新手机。而等待期末考试出成绩，就是异步过程。在这段时间内，作为主线程的小明的妈妈可以去做其他的工作，而负责执行异步过程的小明则要好好学习。

    等到异步过程执行完毕，也就是期末考试出成绩了，就到了**承诺**兑现的时候了。小明和妈妈就会核对小明的学习成果（异步过程的执行状态），如果小明考到了 100 分（异步过程执行成功），作为主线程的小明的妈妈就会去买新手机。而如果小明没考到 100 分（异步过程执行失败），小明的妈妈就不会买新手机。无论买不买，作为主线程的小明的妈妈都做好了异步过程的善后工作。

    换到程序设计的情景。一个**承诺**要成立，就需要规定好，异步过程执行完毕，主线程需要做什么，其实这就是指派异步过程的回调函数。在 `Promise` 的构造函数之中，`resolve` 实际上就是“考到 100 分，小明的妈妈要买新手机”，`reject` 实际上就是“考不到 100 分，小明的妈妈不买新手机”，函数体就是“小明努力学习”。

    但是构造函数之中虽然有了 `resolve` 和 `reject`，但实际上我们还没有具体规定这两个回调到底是啥。而指定这两个回调需要调用 `Promise` 的 `then` 和 `catch` 方法。这个我们在下面讲解。

给 `resolve` 和 `reject` 指定具体的函数体需要使用 `then` 和 `catch` 方法。这两个方法都会接受回调函数，并且返回 `Promise` 对象：

```javascript
new Promise((resolve, reject) => {
    let asyncSucceeded = Math.random() > 0.2;
    setTimeout(() => {
        if (asyncSucceeded) {
            resolve("Async succeeded!");
        } else {
            reject("Async failed!");
        }
    }, 1000);
})
    .then((res) => {
        console.log("Then");
        console.log(res);
    })
    .catch((err) => {
        console.log("Catch");
        console.log(err);
    });
```

通过这样的方式，我们就在 `Promise` 构造函数接受的回调函数的函数体中规定了异步过程的具体任务，在 `then` 和 `catch` 方法之中指派了异步过程的回调函数。这样，主线程就完整完成了异步过程的派遣。

`Promise` 比直接写回调模式优越的地方就是这种写法解决了回调地狱。我们依然考虑依次获取多数据源数据的业务场景，由于 `then` 方法依然返回 `Promise` 对象，所以实际上我们可以在 `then` 后面接着调用 `then`。而上一个 `then` 之中的回调函数的返回值会被传入下一个 `then` 之中的回调函数的第一个参数：

```javascript
new Promise((resolve, reject) => {
    // Get Data #1
    resolve("Data #1");
})
    .then((res) => {
        console.log(res);
        // Get Data #2
        return "Data #2";
    })
    .then((res) => {
        console.log(res);
        // Get Data #3
        return "Data #3";
    })
    .then(console.log)
    .catch(console.log);
```

这种写法被称为 `then` 链，其好处是比回调函数写法更易读且易维护。

另外，对于多依赖问题（需要多个异步全部结束后才能派遣下一个异步），`Promise` 提供了 `all` 方法：

```javascript
Promise.all(
    [1, 2, 3].map((val) => {
        return new Promise((resolve, reject) => {
            resolve(`Promise #${val}`);
        });
    })
)
    .then((res) => {
        console.log(res); // ["Promise #1", "Promise #2", "Promise #3"]
    })
    .catch((err) => {});
```

这里 `then` 之中的回调函数接受的参数是所有 `Promise` 对象 `resolve` 回调所接受的参数拼成的数组，`catch` 同理。

---

另外，注意两点：

- `Promise` 一旦创建，内部的异步过程就开始执行，且开始执行后就不会取消
- 创建 `Promise` 会写很多很长的回调，写完这些回调后很容易给编写者一种“异步已经执行结束”的错觉。实际上这么长的语句仅仅是**定义了 `Promise` 对象**，也就是仅仅只是派遣了异步。

关于第二点，可以看下面的代码：

```javascript
let i = 1;

new Promise((resolve, reject) => {
    resolve(2);
})
    .then((res) => {
        i = 2;
    })
    .catch(console.log);

console.log(i); // 1
```

这里创建完 `Promise` 对象之后立刻输出 `i`，由于此时异步还没有执行完毕，也就是还没有将 `i` 重新赋值，所以只能得到原先值 `1`。

!!! caution "编者曾经踩过的坑不能让读者再踩"

    在实际工程中，创建 `Promise` 对象的语句可以比这个示例长很多，甚至可以超出一个屏幕显示的范围。此时一定要保持清醒，这么长的语句，**仅仅是派遣了异步**，不要紧接着后面使用一些会被异步过程修改的值。

    跟着我念，**再长的语句也仅仅是派遣了异步**，**再长的语句也仅仅是派遣了异步**，**再长的语句也仅仅是派遣了异步**。

    此外，实际工程之中，一般会设计一个标记表示某个异步是否还在执行。这个标记会在创建 `Promise` 对象派遣异步的时候置真，然后在 `then` 链最后的回调中置假。这样主线程就可以根据这个标记确定是否可以使用一些敏感变量（比如上面例子中的 `i`）。

    这种思想其实类似于读写锁的思想。

### `async` 与 `await`

在最新标准之中，JavaScript 引入了 `async, await` 这两个关键字，这两个关键字的作用是能够让异步代码写得和同步代码一样自然。

我们可以用 `async` 关键字将一个函数声明为异步函数。调用异步函数的时候，其会立刻返回并派遣一个异步：

```javascript
const foo = async () => {
    setTimeout(() => {
        console.log("Async over!");
    }, 1000);
};

foo();
console.log("Sync code here!");
```

而 `async` 关键字的的另外一个作用就是将函数的返回值包装为 `Promise` 对象：

```javascript
const foo = async () => {
    setTimeout(() => {
        console.log("Async over!");
    }, 1000);
};

typeof foo(); // "object", note "undefined"
```

那么我们也可以按照 `Promise` 的 `then` 链写法使用异步函数：

```javascript
const foo = async () => {
    return "Data #1";
};

foo()
    .then((res) => {
        console.log(res);
        return "Data #2";
    })
    .then(console.log)
    .catch(console.log);
```

`await` 关键字后面可以接一个变量，如果这个变量不是 `Promise` 对象，那么 `await` 关键字不产生任何效果。

如果是 `Promise` 对象，那么 `await` 关键字会阻塞代码运行，直到这个 `Promise` 对象代表的异步执行完毕。

如果异步成功，这个时候 `await` 语句的返回值是 `Promise` 的 `resolve` 回调接受的参数，无论有没有通过 `then` 方法指定 `resolve` 回调。

如果异步失败，首先确定这个 `Promise` 有没有通过 `catch` 方法规定 `reject` 回调。如果有，`await` 返回 `catch` 之中回调函数的返回值，如果没有，抛出 Uncaught Failure 错误。

比如说：

```javascript
let a = await 1;
a; // 1

let b = await new Promise((resolve, reject) => {
    let succeeded = Math.random() > 0.2;
    if (succeeded) resolve("Success");
    else reject("Failure");
})
    .catch((err) => "Error " + err);
b; // Maybe "Success", maybe "Error Failure"
```

`async, await` 关键字实际上是针对 `then` 链可能过长导致可读性降低的问题而提出的。我们现在还是考虑依次从多个数据源获取数据的问题，事实上通过 `async, await` 关键字，这个异步过程甚至可以写得很像同步代码：

```javascript
const fetchData = async () => {
    let data_1 = await fetchData_1(initData);
    let data_2 = await fetchData_2(data_1);
    let data_3 = await fetchData_3(data_2);
    return [data_1, data_2, data_3];
}
```

这里的 `fetchData` 系列函数均是异步函数。

可以发现这种写法和同步代码几乎没有什么差别，可读性比 `then` 链好一些，更是比回调函数写法好很多。

另一方面，其解决了不定长 `then` 链的问题。如果要依次拉取数据的信息源个数不是固定的，而是通过变量指定，那么 `then` 链就不可用了，因为我们不知道具体要写多少个 `then`。但是通过 `async, await` 结合 `for` 循环就可以解决问题。

---

但是 `async, await` 带来的争议也不小，最大的危险是异步传染。

`await` 关键字的使用有一个要求，也就是如果在函数中使用，那么这个函数必须是异步的。原因也是好理解的，因为 `await` 会阻塞代码运行，如果在主线程上阻塞，则会导致页面卡顿，这本来就和异步的初衷矛盾。

那么进一步，调用了这个函数的外层函数中的语句为了防止异步可能带来的敏感变量问题（和 `Promise` 问题类似，异步函数如果修改了某一个变量，这个变量很有可能不会被及时修改），外层函数也很有可能给这个函数加上 `await`，这又会导致外层函数不可抗力地变为异步函数。这种传染会导致整片函数变为异步，从而让逻辑变得混乱。

另外，`async, await` 关键字会降低我们对阻塞的敏感，从而编写出不必要的阻塞和串行代码。实际上，`async, await` 只是让**依次**拉取信息的代码变得简洁，对于本就可以多并发的信息拉取，反而会有让我们写出不必要的串行代码的可能，从而降低代码效率：

```javascript
const fetchData = async () => {
    let data_1 = await fetchData_1();
    let data_2 = await fetchData_2();
}
```

如果 `data_1, data_2` 本就可以并发获取，这样的写法反而会降低效率，因为这样代码的实际含义是串行地获取数据。真正的写法应该是：

```javascript
const fetchData = async () => {
    let data_1_promise = fetchData_1();
    let data_2_promise = fetchData_2();
    let data_1 = await data_1_promise;
    let data_2 = await data_2_promise;
}
```

只有先获取两个异步的 `Promise` 对象，同时派遣出两个异步后后分别 `await` 才能实现并发。

但是 `async, await` 的目的本来就是用于隐藏掉 `Promise` 的存在，将异步代码写成同步的格式，这样做其实还不如直接把 `Promise` 写出来。

此外，对于多依赖问题，`async, await` 关键字并没有解决，因为 `await` 一次只能等待一个异步。这个时候还是不可避免地需要使用 `Promise` 对象。

!!! note "该如何写好异步代码"

    现代的 JavaScript 已经将异步操作封装得足够易用，借助 `Promise, async, await` 就可以写出很明晰地异步代码。

    但是我们在编写的时候一定需要注意自己写的不是同步代码而是异步代码，以免出现问题。此外，应当根据实际应用要求合理选择直接使用 `Promise` 对象还是 `async, await` 关键字。

    实际上文档能教给读者的很有限，编者还是希望大家多去编写真实的工程代码以真正掌握 JavaScript 异步。

## JavaScript 的面向对象进阶

!!! note "一些可能不必要的知识"

    本章节将主要讲解 JavaScript 面向对象编程的原型链逻辑和私有方法的模拟，但这些知识对于 JavaScript 的初学者并不一定必要。所以编者建议如果只是想学习 JavaScript 基础语法后直接转 TypeScript 以及上手前端框架，则不需要浪费时间学习这一部分。想要深入了解这一门语言的读者，编者建议一定要学习这一部分。

## 后续拓展

JavaScript 是应用相当广泛的一门脚本语言，大量的 Web 应用或者移动端应用依靠 JavaScript 以及其衍生语言开发。而且作为“前端三件套”（指 HTML、CSS 和 JavaScript）之一，学习 JavaScript 对编写前端工程的重要性不言而喻。

后续可以阅读的文档有：

- TypeScript 语言入门 <https://docs.net9.org/language/typescript>

---

可以应用 JavaScript 语言相关知识的课程：

- 程序设计训练
- 软件工程

## 资源链接

- 廖雪峰 JavaScript 语言教程 <https://www.liaoxuefeng.com/wiki/1022910821149312>
- Mozilla JavaScript 语言教程 <https://developer.mozilla.org/zh-CN/docs/Web/JavaScript>
- JSFuck 官方网站 <http://www.jsfuck.com/>