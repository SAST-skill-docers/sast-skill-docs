## JavaScript 的变量与变量类型

### 变量声明

JavaScript 语言之中声明变量的关键字有三个，分别为 `var, let, const`。从字面意义上来看，`const` 用于声明常量，而 `var, let` 则用于声明变量。在 `var, let` 选取的问题上，编者建议尽量全部使用 `let` 关键字声明变量以防止混乱，具体原因可以参照讲解 JavaScript 函数部分的**变量提升**注解。

在运行中，直接使用未出现过的变量相当于 `var` 声明，但这是很不好的编码习惯。

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

JavaScript 语言支持七种基本类型，即数字、大整数、字符串、布尔值、`symbol` 类型、`undefined` 类型和 `null` 类型。前五个类型是容易理解的，而后两者则会在最后讲解。

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

JavaScript 语言之中的数字不区分整数和浮点数，统一使用浮点数表示。仅当在 `Number.MAX_SAFE_INTEGER` 和 `Number.MIN_SAFE_INTEGER` 之间的整数运算是安全的，否则将会是使用双浮点数的近似值。所以除法没有类似 C/C++ 的向下取整的性质：

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

JavaScript 可以任意使用单双引号来表示字符串。JavaScript 中的 `string` 是原始值，也即 `string` 是不可改变的，这与 Python 类似，对 `string` 的任何操作会返回新的 `string` 值，而不是对旧的值做了部分修改。

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

当然，最好不要在同一段代码中混用单双引号，也不要用反引号写非模板字符串。

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

##### `bigint` 类型 (ES 2020 新增)

```javascript
let a = 9007199254740991n;
```

顾名思义，`bigint` 类型用于存储和计算超过 `number` 类型限制的大数。

需要注意的是，JavaScript 中 `number` 能表示的最大值约为 1.7976931348623157e+308，继续增加会得到 `Infinity`，且超出 9007199254740991 的数字的计算就不再是精确的。

##### `symbol` 类型 (ES 6 新增)

```javascript
let sym = Symbol('SAST');
```

`symbol` 是 JavaScript 中非常独特的值，`symbol` 值只能通过调用 `Symbol()` 构造，传入的参数除用于调试外无其他意义，**该函数每次调用都会返回不同的 `symbol` 值**，故 `symbol` 类型值的唯一作用就是作为独一无二的标识符。

值得注意的是 `symbol` 值是**可以哈希**的，这也使得 `symbol` 成为了仅有的三种可以作为对象的键的类型（另外两种是 `number` 和 `string`，但实际上 `number` 会被当作 `string` 处理）。

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

每一个键值对之中的键称为这个对象的**属性**。访问给定对象的属性使用 `.` 或 `[]` 运算符：

```javascript
let obj = { foo: 1, };
obj.foo; // 1
obj['foo']; // 1
```

为了方便查看更多细节，我们使用 Chrome 浏览器运行上述代码，当我们在控制台中尝试输出：

```javascript
obj.__proto__
```

!!! caution "废弃警告"

    `__proto__` 是即将废弃的属性，并且和 Python 类似，其名称使用双下划线包围实际上就代表了其理应私有，不能直接访问。但出于演示的目的我们仍然可以查看它的属性。

我们将会看到变量 `obj` 上已经定义了一系列方法：

```javascript
constructor: ƒ Object()
hasOwnProperty: ƒ hasOwnProperty()
isPrototypeOf: ƒ isPrototypeOf()
propertyIsEnumerable: ƒ propertyIsEnumerable()
toLocaleString: ƒ toLocaleString()
toString: ƒ toString()
valueOf: ƒ valueOf()
__defineGetter__: ƒ __defineGetter__()
__defineSetter__: ƒ __defineSetter__()
__lookupGetter__: ƒ __lookupGetter__()
__lookupSetter__: ƒ __lookupSetter__()
__proto__: (...)
get __proto__: ƒ __proto__()
set __proto__: ƒ __proto__()
```

这是由于我们创建的对象默认是使用构造函数 `Object` 创建的，这就使它可以访问**原型**上定义的方法，关于原型的更多细节，将会在[JavaScript 的面向对象进阶](../oop-advance)中详细展开。

!!! note "对象的原型"

    JavaScript 的一切对象都有它的原型，通过在原型上定义方法可以使得以该对象为原型的对象都可以访问这些方法。通常而言，我们只需要了解如何构造特定原型的对象，并使用该原型的方法即可。

对象的属性名可以是**任何有效的 JavaScript 字符串或 `symbol` 值**，但如果变量名不是合法的 JavaScript 标识符（例如包含空格等特殊字符、或使用 `symbol` 等），就只能使用 `[]` 来访问：

```javascript
const sym = Symbol();
const obj = {
    [sym]: 1,
    valid: 2,
    'invalid identifier': 3,
};

console.log(obj[sym]);
console.log(obj.valid);
console.log(obj['invalid identifier']);
```

需要注意的是，JavaScript 可以接受数字作为属性名，但实际上**数字被转换为字符串使用**（例如在下面的 `Array` 中），为了避免出现意料之外的访问，建议总是使用确定的类型访问对象的属性。

```javascript
const obj = {};
obj['1'] = 'value1';
obj['2'] = 'value2';
console.log(obj[1]);    // 可能出现非预期的访问，但 JavaScript 并不会报错
console.log(obj['1']);
```

!!! note "对象的可变性"

    需要注意的是，虽然可以使用 `const` 声明对象常量，但这指的是这一变量名始终指向该对象，而非该对象的属性不可变，你可以使用 `const` 声明对象并任意修改它的属性，如果需要添加不可修改或不可删除的属性，可以使用 [`Object.defineProperty`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 方法。

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

如果尝试输出

```javascript
arr.__proto___
```

会发现变量 `arr` 有更多的方法和属性，其中我们注意到 `constructor` 为 `Array()`，也即数组的构造函数是 `Array`；另外其中有一个属性为 `length`，表示数组的长度。

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

##### 其他内置对象

除了按照上述的方法构造对象外，JavaScript 还提供了很多内置对象，这使得我们可以很容易地构造一些常用的数据结构，或构造包含特定方法的对象。以下介绍几种最常见的内置对象的构造，更多的内置对象以及方法请参考 [JavaScript 标准内置对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects)。

- 正则表达式

    通过以下方法构造正则表达式：
    
    ```javascript
    let reg = /sast/;
    ```

    正则表达式的构造函数为 `RegExp`。

- 日期

    通过以下方式构造 `Date` 对象并获取当前时间：
    
    ```javascript
    let date = new Date();
    console.log(date.toLocaleString()); // => 2022/7/23 12:11:01
    ```

    `Date` 对象的构造函数是 `Date`。

- 集合

    通过以下方式构造集合：

    ```javascript
    let docs = new Set();
    docs.add('JavaScript');
    ```

    集合对象的构造函数是 `Set`。

- 其他

    并非所有的内置对象都是用来构造对象，一些内置对象只用作存储一类方法或值，称为**命名空间对象**，例如：

    ```javascript
    // 数学常数和常用函数
    Math.PI;           // => 3.141592653589793
    Math.floor(5 / 2); // => 2
    Math.sin(Math.PI); // => 1.2246467991473532e-16

    // 序列化和反序列化 JSON
    JSON.stringify({ sast: 'yyds' }); // => '{"sast":"yyds"}'
    JSON.parse('{"sast":"yyds"}');    // => { sast: 'yyds' }

    // 控制台对象
    console.log('Hello, world');
    console.error('Some error occurred!');
    ```

##### 展开语法 (Spread Syntax)

在构造对象、数组字面量和函数调用时，可以将数组和对象展开，如下所示：

```javascript
// 构造对象
let obj1 = { foo: 'bar', x: 42 };
let obj2 = { foo: 'barrrr', y: 13 };
let clonedObj = { ...obj1 };            // 与 obj1 完全相同
let mergedObj = { ...obj1, ...obj2 };   // 包含两者的内容，相同的键后出现的会覆盖先出现的
let halfClonedObj = { ...obj1, x: 13 }; // 也可以另外覆盖或添加部分内容

// 构造数组
let arr1 = [1, 2, 3];
let arr2 = [...arr1, 4, 5];   // => [1, 2, 3, 4, 5]

// 函数调用
console.log(...arr2);  // => 1 2 3 4 5
```

##### 解构赋值

通过解构赋值可以将属性/值从对象/数组中取出，赋值给其他变量：

```javascript
let a, b, rest;
[a, b] = [10, 20];  // => a = 10, b = 20
[a, b] = [b, a];    // => a = 20, b = 10

[a, b, ...rest] = [10, 20, 30, 40, 50]; // => a = 10, b = 20, rest = [30, 40, 50]

// 这里最外层的括号是为了消歧义，防止将大括号认为是代码块
({ a, b } = { a: 10, b: 20 }); // => a = 10, b = 20
({ a, b, ...rest } = { a: 10, b: 20, c: 30 }); // => a = 10, b = 20, rest = { c: 30 }
```

可以忽略一部分值，也可以提供默认值：

```javascript
let a;
[, a] = [10, 20];           // => a = 20
({ a } = { a: 10, b: 20 }); // => a = 10

[, a = 20] = [10];        // => a = 20
({ a = 10 } = { b: 20 }); // => a = 10
```

解构对象时还可以重命名：

```javascript
let { b: a } = { b: 10 };   // => 声明变量 a = 10，没有声明变量 b
```

以上方法还可以嵌套使用：

```javascript
let a: number;
({ b: { c: { d: a } } } = { b: { c: { d: 10 } } });  // => a = 10
```

##### 对象字面量的简记

从 ES 6 开始支持以下简记：

```javascript
let a = 1;
let b = { a };  // 等价于 let b = { a: a }
```

##### 其他

需要注意的是，JavaScript 中各种数据类型（尤其是内置对象）上实现了丰富的方法，熟悉使用这些方法可以帮助你写出更好的代码，一些数组方法的示例将在 [JavaScript 的函数](../function) 中给出。

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

对于不便使用 `.` 运算符的索引，如 `number`，可以使用 `?.[]`：

```javascript
let arr1 = [1, 2, 3];
let arr2;               // 此时 arr2 的值为 undefined
console.log(arr1[1]);   // => 2
console.log(arr2[1]);   // TypeError: Cannot read properties of undefined (reading '1')
console.log(arr2?.[1]); // => undefined
```

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

这一部分可以参考 React 相关文档。

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

    !!! note "JavaScript 中的 Falsy 值和 Nullish 值"
    
        你可能注意到在取值时 `||` 和 `??` 的行为比较类似，二者的区别在于 `||` 判断逻辑假，而 `??` 判断空值。

        JavaScript 中的假（Falsy）值有：

        - `false`
        - `0`
        - `-0`
        - `0n` (`bigint`)
        - `''`, `""`, ` `` `
        - `null`
        - `undefined`
        - `NaN`

        而空（Nullish）值只有 `null` 和 `undefined` 两个。

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

    JavaScript 由于缺少类型检查和一些强制的编码要求，可能在不经意间写出难以理解的代码，使得一些技巧看起来像魔法。但需要强调的是，这些看似炫技的内容绝非正常工程中应当出现的内容，在阅读这部分之前，请首先掌握正确的编码习惯，请不要在日常开发中使用以下内容。

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
