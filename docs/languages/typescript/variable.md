# TypeScript 的变量与变量类型

TypeScript 最大的特点就是引入了类型系统，这样就可以在编译为 JavaScript 代码之前由编译器进行类型检查。在这样的条件下，TypeScript 中的变量在声明的时候就可以指定类型，编译器在将 TypeScript 代码编译为 JavaScript 代码的时候会进行类型检查，若有不符合类型声明的情况则会报错：

```typescript
const foo = (name: string): void => {
    console.log("Hello, " + name);
}

foo(2); // Error!
```

## 变量类型

由于 TypeScript 是 JavaScript 的超集，所以其基础变量类型依然和 JavaScript 一致。

声明变量时可以在变量后面标注，也可以根据初始值自动推断，但如果声明变量时不赋初始值，则必须添加类型标注，否则在使用时会报错（即自动推断该变量为 `undefined` 的类型，因此不能赋其他值）。

常用的基础类型包括：

```typescript
let isDone: boolean = false;
let clickCnt: number = 6;
let greeting: string = "Hello!";
```

### 原始值

1. 布尔类型（`boolean`）：

    ```typescript
    let sast_yyds: boolean = true;
    ```

2. `null` 类型：

    ```typescript
    let null_value: null = null;
    ```

3. `undefined` 类型：

    ```typescript
    let undefined_value: undefined = undefined;
    ```

4. 数字类型（`number`）：

    ```typescript
    let sast_rank: number = 1;
    ```

5. `bigint` 类型（ES 2020 新增）：

    ```typescript
    let a: bigint = 9007199254740991n;
    ```

6. 字符串类型（`string`）：

    ```typescript
    let course: string = 'TypeScript introduction';
    ```

7. 符号类型（`symbol`，ES 6 新增）：

    ```typescript
    let sym: symbol = Symbol('SAST');
    ```

- 尽管 TypeScript 会尝试阻止你这么做，但 JavaScript 是动态类型语言，你可以将一个变量赋为其他类型值。
- 尽管 TypeScript 更加会尝试阻止你，但 JavaScript 是弱类型语言，要小心随处可能存在的隐式类型转换，这可能会引起很难理解的行为，关于这一现象的详细解释可以参考 [JavaScript 的变量与变量类型](../../javascript/variable)中的“JavaScript 的魔法”一节。

    ```typescript
    console.log(1 + null);      // => 1
    console.log(1 + undefined); // => NaN
    ```

## 类型标注

### 简单类型标注

上文已经提到原始值的类型标注方式，但需要注意的是 TypeScript 允许使用字面量作为类型标注，如：

```typescript
let one: 1 = 1;
one = 2;   // Error!
```

这里变量 `one` 的类型被限定为字面量 `1` 而不是所有的 `number`，这种标注的作用在下面会展示。

当你不知道该标记什么类型，或者你希望可以写任何类型时，可以谨慎使用 `any`，编译器将不会尝试对 `any` 类型的变量做任何的分析。

```typescript
let a: any = 'whatever you like';
a = 12345; // OK

let notSure: any = 1;
notSure = "Maybe it is a string"; // OK
```

!!! caution "注意可能的 `any` 类型滥用"

    `any` 类型是目前 TypeScript 语言之中具有较大争议的一个设计，因为理论上我们可以将所有的变量声明为 `any` 从而绕过类型检查，这个时候 TypeScript 实际上退化为 JavaScript。

    但是考虑到目前 Web 前端项目会引用大量的第三方库，开发者很多时候无法完全把握某些变量的信息，所以 `any` 类型是必要的。不过我们需要注意其使用，对于能够给定类型的变量则尽量不标记为 `any`。

    针对这一点，部分代码检查工具已经实装了检查“可疑 `any`”的功能。

#### 类型别名

可以使用 `type` 关键词定义类型别名，在需要实现复杂的（尤其是嵌套的）类型时非常有用：

```typescript
type NumberOne = 1;
let one: NumberOne = 1;
one = 2; // Error!
```

### 对象和数组的类型

对象和数组的类型应当这样标注：

```typescript
// Detailed object
let obj: {
    name: string,
    age: number,
    address: string
} = {
    name: "qiubao",
    age: 57,
    address: "Tsinghua"
};

let sast_summer: {
    frontend: string;
    backend: string;
} = {
    frontend: 'react',
    backend: 'gin',
};
```

```typescript
let sast_summer_frontend: string[] = ['Java/Android', 'JS/TS/React'];

sast_summer.frontend = 'Java/Android'; // success
sast_summer.frontend = ['Java', 'JS']; // Error! 不能将 string[] 值分配给 string 类型的变量
```

对于固定长度和类型的数组（这实际上更接近于其他语言中的元组），可以这样标注：

```typescript
let arr: [number, boolean] = [1, false];
```

### 联合类型和类型收窄

TypeScript 支持将变量声明为若干个类型之中的一种，这一般称为联合类型（Union Type）。
声明联合类型的时候，多个类型之间使用 `|` 隔开：

```typescript
let union: number | string = 7;
union = "string"; // OK
```

联合类型最常用的地方是标注函数参数，这样就允许了函数接受多种类型的参数：

```typescript
const addPrefix = (x: number | string): void => {
    console.log("Prefix " + x);
};

addPrefix(1); // OK
addPrefix("String"); // OK
```

对于上面提到的错误，可以这样解决：

```typescript
let sast_summer: {
    frontend: string | string[];
    backend: string;
} = {
    frontend: 'react',
    backend: 'gin',
};

sast_summer.frontend = ['Java', 'JS']; // Success
```

这里我们声明 `frontend` 可能有两种类型，即 `string` 和 `string[]`，所以我们可以将 `frontend` 任意赋值为其中的一种，但这就导致我们在使用这一值时，不能精确判断上面包含的方法，例如我们尝试执行：

```typescript
sast_summer.frontend.match(/react/);
```

编译器会告诉我们*类型“string[]”上不存在属性“match”*，因此编译器无法确定这行代码是正确的，但如果我们的代码足以使编译器推断出具体类型，如：

```typescript
if (typeof sast_summer.frontend === 'string') {
    console.log(sast_summer.frontend.match(/react/));
} else {
    // sast_summer.frontend as string[]
}
```

编译器就会明白在两个分支上分别是两种类型，并允许调用相应的方法，这种行为被称为**类型收窄**。

#### 联合类型实现枚举类型

上文提到字面量可以作为类型标注，那么使用联合类型，就可以实现枚举行为：

```typescript
type TwoPowers = 1 | 2 | 4 | 8 | 16;
let a: TwoPowers = 2; // OK
let b: TwoPowers = 5; // Error!
```

### 类型断言

当你确信你比编译器拥有关于某一变量类型的更多知识时，可以使用类型断言告诉编译器服从你的断言：

```typescript
let val: any = "A string";
let stringLen: number = (val as string).length;

let sast_summer: {
    frontend: string | string[];
    backend: string;
} = {
    frontend: 'react',
    backend: 'gin',
};

console.log((sast_summer.frontend as string).match(/react/));
```

其中 `as` 关键词表示类型断言，但你不能断言一个变量为明显冲突的类型，如：

```typescript
console.log(sast_summer.frontend as number);  // Error!
```

除非你有绝对的信心时，可以先将类型断言为 `unknown` 再断言为任意其他类型：

```typescript
console.log(sast_summer.frontend as unknown as number);  // OK
```

### `never` 类型与耗尽检查

TypeScript 支持一种特殊的类型，即 `never` 类型。这种类型常被用于标注函数返回值，代表这个函数永远不会终结或者会抛出异常：

```typescript
const neverEnd = (): never => {
    while (true) {}
};
```

这种类型的值永远不能被实例化，也即尝试声明和使用 `never` 类型的值将会总是出现错误，利用个特点，我们可以检测程序是否考虑了所有的情况，这被称为耗尽检查（Exhaustive check）。

例如我们需要编写函数处理联合类型 `number | string | boolean`，其代码可能为：

```typescript
type All = number | string | boolean;
// 关于函数表达式的更多细节将在稍后讲解
// switch 语句用法与 C/C++ 一致
const handler = (value: All) => {
    switch (typeof value) {
        case 'number':
            // do something
            break;
        case 'string':
            // do something
            break;
        case 'boolean':
            // do something
            break;
        default:
            let exhaustiveCheck: never = value;
    }
}
```

根据 `never` 的特点，`default` 分支的代码执行必然会产生错误，因此如果该 `switch` 语句未能穷尽 `typeof value` 的可能取值（而使得代码落入 `default` 分支），就会导致编译器报错。

如果修改类型 `All` 为 `number | string | boolean | undefined`，编译器会告诉我们*不能将类型“undefined”分配给类型“never”*，这就是因为当 `value === undefined` 时，会尝试将 `undefined` 赋给 `never` 类型的变量。
这样，`handler` 函数就会因为没有耗尽所有可能而报错，提醒开发者需要更新这个函数。
这种处理方式类似于 C++ 程序在不可能的分支上加入 `assert(false)` 进行检测。
