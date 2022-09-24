# TypeScript 的函数

## 函数声明和调用

我们一般把 TypeScript 的函数也认为是一种变量，因为在 TypeScript 之中，函数的行为很大程度上和变量类似。

TypeScript 语言之中声明一个函数的语法为：

```typescript
function sum(x: number, y: number): number {
    return x + y;
}
```

这里要注意的是，编译器会尝试推断函数返回值，但**不会尝试从函数实现中**推断参数类型，因此参数列表的类型标注是必不可少的。

也可以按照声明变量的方式：

```typescript
let sum = function (x: number, y: number): number {
    return x + y;
}
```

这里 `sum` 作为一个变量，它的类型并非前文所提过的原始值，因此也是一个对象，它的构造函数是 `Function`，而它的类型可以标注为：

```typescript
let sum: (x: number, y: number) => number = function (x, y) {
    return x + y;
}
```

可以看到这种写法可以使编译器推断出参数列表和返回值的类型。

函数调用的语法和大多数语言相同：

```typescript
console.log(sum(1, 2)); // => 3
```

### TypeScript 函数的参数列表

实际上 TypeScript 在运行时不会检查你函数调用是不是符合参数列表（尽管在编译器会尝试进行静态检查，但是如果你使用 `any` 或其他一些方法传入其他类型参数，仍然会继续运行），实际上 JavaScript 还不检查你函数调用的时候传入的参数个数（但 TypeScript 会阻止传入个数错误的参数）。

在 TypeScript 中可以通过将参数标为可选或提供默认值来允许不同长度的传入参数：

```typescript
function sum(x: number, y: number = 1, z?: number): number {
    return x + y + (z ?? 0);  // 这里 y 不可能为空值，但 z 可能
}

console.log(sum(1));        // => 1 + 1 + (undefined ?? 0) = 2
console.log(sum(1, 0));     // => 1 + 0 + (undefined ?? 0) = 1
console.log(sum(1, 2, 3));  // => 1 + 2 + (3 ?? 0) = 6
```

需要注意的是可选参数或提供默认值的参数必须位于其他参数之后，考虑到 TypeScript 实际上不会检查传入参数的类型，为了不产生混淆，这种限制是必要的。

### 箭头函数

一种更简洁的函数表达式的记号是这样的：

```typescript
// 以下几种写法的结果相同
const sum = (x: number, y: number) => x + y;
const sum = (x: number, y: number) => {
    return x + y;
};
function sum(x: number, y: number) {
    return x + y;
}
```

当函数体只有一个返回语句时，可以省略外层的括号和 `return` 关键字，若只有一个参数（且编译器能够推断类型时），可以省略参数列表的小括号：

```typescript
const plusOne = x => x + 1;
```
