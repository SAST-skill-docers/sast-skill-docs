# Rust 中的引用

## 可变与不可变引用

Rust 用 `&` 运算符来创建变量的引用，用 `*` 运算符来解引用

```rust
let a = 1; // a: i32
let b = &a; // b: &i32
println!("{}", *b); // 1
```

与声明变量类似，引用默认也是不可变的，用 `&mut` 来创建可变引用。

```rust
let mut a = 1; // a: i32
let b = &a; // b: &i32
*b = 1; // b 是不可变引用，编译不通过
```
```rust
let mut a = 1;
let b = &mut a; // b: &mut i32
*b = 2; // b 是可变引用，编译通过
```
