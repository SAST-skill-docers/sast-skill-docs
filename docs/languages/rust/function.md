# Rust 中的函数

## 函数声明

Rust 中通过 `fn` 关键字声明函数，在函数名后的括号内声明参数（必须指定类型），在 `->` 后面指定返回类型（如果没有，可以不加）。

```rust
fn func_1() {
    println!("Hello");
} // 无参数，无返回值的函数

fn func_with_param(i: u32, j: u32) {
    println!("I love you {} times", i + j);
} // 有参数的函数

fn func_with_return_value(i: i32, j: i32) -> i32 {
    return i + j;
} // 有返回值的函数

fn func_with_return_value_mod(i: i32, j: i32) -> i32 {
    i + j
}
// 在有返回值的函数中，return 语句并不是必需的。在这里
// 由大括号包裹的代码块是一个表达式，i + j 就是表达式的值，
// 同样也就是函数的返回值。
```

!!! note "与 C++ 的对照"

    在 C/C++ 中，函数的声明的类型在前，没有额外关键字。读者可能需要一些时间来适应 Rust 的语法。

## 函数调用

Rust 的函数调用语法与 C/C++ 没有太大区别。

```rust
let a = 1;
let b = 2;
let c = func_with_return_value(a, b);
```
