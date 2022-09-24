# TypeScript 的复杂类型

## 泛型

与其他一些支持泛型的语言不同，TypeScript 的泛型并不会为每一种类型生成一份代码，只是检查是否每一种可能的类型都可以兼容，在运行中仍然使用同一份代码。

可以在声明函数、类、接口和类型时声明泛型：

```typescript
function identity<Type>(arg: Type): Type {
    return arg;
}

const identity = <Type>(arg: Type): Type => arg;

class GenericNumber<NumType> {
    zeroValue: NumType;
    add: (x: NumType, y: NumType) => NumType;
}

interface Request<ReqBody, ResBody> {
    request: ReqBody;
    response: ResBody;
}

type MaybeArray<Value> = Value | Value[];
```

可以通过以下方式使用前面定义的泛型：

```typescript
identity<number>(1);

const num = new GenericNumber<number>();

const req: Request<{ action: string }, { result: string }> = {
    request: {
        action: 'update system',
    },
    response: {
        result: 'succeeded',
    },
};

let nums: MaybeArray<number> = 0;
nums = [0, 1, 2];
```

可以为泛型添加限制和默认值，也可以由编译器推断泛型类型：

```typescript
function identity<Type = number>(arg: Type): Type {
    return arg;
}

identity(1);

function identity<Type extends { data: string }>(arg: Type): string {
    return arg.data;
}

identity({ data: '' });  // inferred Type = { data: string }
```

需要注意的是泛型只能使用类型作为参数，如果需要某个值的类型，可以使用 `typeof`。

## `keyof` 和 `typeof` 关键字

`typeof` 除了可以作为运算符获取变量类型以外，用作类型标注时，可以获取变量的具体类型（而非作为运算符时的有限种类）：

```typescript
const someObj = {
    foo: 1,
    bar: '2',
};

// inferred arg: { foo: number; bar: string }
function f(arg: typeof someObj) {
    /* do something */
}
```

而 `keyof` 可以获取**类型**的键的类型（作为枚举）：

```typescript
const someObj = {
    foo: 1,
    bar: '2',
};

// inferred arg: 'foo' | 'bar'
function f(arg: keyof typeof someObj) {
    /* do something */
}
```

但 TypeScript 的一个缺陷是，使用 `for...in` 语法时并不会自动推断为 `keyof`，例如：

```typescript
const someObj = {
    foo: 1,
    bar: '2',
};

for (const key in someObj) {
    // inferred key: string, value: any
    const value = someObj[key];
}
```

## 访问类型内部

通过这种方式可以获取一个类型中的部分内容：

```typescript
interface SomeComplexType {
    foo: {
        bar: string;
    }[];
}

type Foo = SomeComplexType['foo']; // Foo = { bar: string }[]
// 以下两种等价
type Bar = Foo[number];            // Bar = { bar: string }
type Bar = Foo[0];                 // Bar = { bar: string }
```

## 条件类型

可以使用类似三目运算符的语法来编写条件类型：

```typescript
interface Animal {
    live(): void;
}

interface Dog extends Animal {
    woof(): void;
}
 
type Example1 = Dog extends Animal ? number : string;    // Example1 = number
 
type Example2 = RegExp extends Animal ? number : string; // Example2 = string
```

这种语法与泛型结合尤为有用：

```typescript
type MessageOf<T> = T extends { message: any } ? T['message'] : unknown;

interface Email {
    message: string;
}
 
interface Dog {
    bark(): void;
}
 
type EmailMessageContents = MessageOf<Email>;  // EmailMessageContents = string
 
type DogMessageContents = MessageOf<Dog>;      // DogMessageContents = unknown
```

## 只读、可选

通过以下方式标记某一属性为只读或可选：

```typescript
interface SomeType {
    readonly readonlyProperty: string;
    optionalProperty?: string;
}
```

需要注意的是，这些修饰只会在编译时检查是否违反，在编译后运行时你仍然可能打破这些限制，在使用 JavaScript 调用 TypeScript 库时尤其可能存在。

## 实用内置类型

TypeScript 内置了多种实用类型，可以帮助我们更简洁地标注一些复杂类型，以下仅做部分展示：

```typescript
interface Data {
    key1?: string;
    key2: string;
}

// 以下相同名称的类型内容相同

// 全部属性变为可选
type PartialData = Partial<Data>;
type PartialData = {
    key1?: string;
    key2?: string;
};

// 全部属性变为必选
type FullData = Required<PartialData>;
type FullData = {
    key1: string;
    key2: string;
};

// 全部属性变为只读
type ReadonlyData = Readonly<Data>;
type ReadonlyData = {
    readonly key1?: string;
    readonly key2: string;
};

// 特定类型之间的映射
type DataDict = Record<string, Data>;
type DataDict = {
    // 这种语法表示任意 string 的值 key 都可以作为键
    [key: string]: Data;
};

// 选择部分属性
type DataKey1 = Pick<Data, 'key1'>;
// 忽略部分属性
type DataKey1 = Omit<Data, 'key2'>;
type DataKey1 = {
    key1?: string;
};

function f(a: number, b: string, c: number[]): number {
    return 0;
}
// 获取参数列表
type Args = Parameters<typeof f>;
type Args = [a: number, b: string, c: number[]];

type Ret = ReturnType<typeof f>;
type Ret = number;
```
