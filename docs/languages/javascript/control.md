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

值得注意的是 `for` 循环除了经典 C 风格外还可以实现类似 Python 的迭代方法。

### `for...in` 循环

`for...in` 语句以**任意顺序**迭代一个对象的除 `symbol` 以外的**可枚举**属性，包括继承的可枚举属性。关于可枚举性可参考[属性的可枚举性和所有权](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)，继承将在对象部分展开讲解。

简单而言，使用 `for...in` 语句可以遍历对象的键：

```javascript
const obj = { a: 1, b: 2 };
for (const key in obj) {
    console.log(`${key}: ${obj[key]}`);
}
// a: 1
// b: 2
```

虽然这种方法也可以遍历数组的索引，但这并不是好的编码习惯，迭代数组应当使用数组的 `forEach` 方法或使用 `for...of` 语句。

### `for...of` 循环

`for...of` 循环在可迭代对象（如 `Array`、`Map`、`Set`、`String`，自己创建可迭代对象可以参考[迭代协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)）上创建迭代循环，最常见的是对数组元素进行迭代：

```javascript
let frontend_courses = ['Java/Android', 'JS/TS/React'];
for (const course of frontend_courses) {
    console.log(course);
}
// Java/Android
// JS/TS/React
```

### 异常处理

JavaScript 的异常处理与 C++ 相似：

```javascript
try {
    someDangerousFunction();
} catch (err) {
    console.log('error occurred');
}
```

### 断句

JavaScript 句末可以加分号也可以不加，但为了安全和便于阅读，建议总是在句末加分号。你可能会好奇为何会出现不安全的情况，请看这一示例：

```javascript
// 假定 f 是一个函数，函数可以赋值给变量，这个将在下一节展开
let a = 1
let g = f
(a)
```

虽然单独写一个 `(a)` 看起来毫无意义，但这理论上是合法语句，只不过没有产生任何副作用，但实际上至少在 Chrome 中运行时，解释器会尝试将 `(a)` 理解为对 `f` 的调用，而此时 `g` 的结果就变成了 `f(a)`，同时由于 `f` 被调用一次，还会产生副作用，但这显然不是我们所期望的，因此我们在每一行末尾加分号来避免这种现象。

但并非所有问题都可以通过加分号解决，另外可能出现的一种情况是，JavaScript 允许在函数返回时不返回任何内容（即 `undefined`），如果写出这样的代码：

```javascript
function test() {
    return
        { a: 1 };
}
```

你可能已经见过这种写法，在返回值很复杂的时候适当换行不失为一种保持美观性的方法，然而在 JavaScript 中，由于允许返回空内容， `return` 自身又足以作为一条语句，又由于句末不需要加分号，在一些执行环境中上述代码会直接返回空值，而不会尝试解析下一行内容，这显然也不是我们所期望的。解决这一问题的一种方法是：

```javascript
function test() {
    return {
        a: 1
    };
}
```

由于单个的括号不能作为语句结束，解释器会继续向下解析直到遇到分号处。

如果你还记得上一节中我们解构赋值一个对象时在最外层添加了小括号，这是因为在句首遇到括号时，解释器会优先认为这是一个代码块的开头，而非一个解构赋值语句，而后面的部分是不可以作为代码块理解的，从而导致执行错误：

```javascript
let { a } = { a: 1 }; // 这里有 let 关键词，解释器不会将括号理解为代码块
{ a } = { a: 2 };     // Error!
({ a } = { a: 3 });   // 添加小括号后解释器将小括号内的内容视为一个（赋值）表达式，而不是代码块
```

类似的问题在箭头函数中同样存在，我们将在下一节看到。
