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
