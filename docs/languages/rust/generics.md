# Rust 的泛型

Rust 中的泛型通过如下方式实现：

## 泛型函数
```rust
fn foo<T, U>(x: T, y: U) {
}
```

## 泛型枚举
```rust
enum Result<T, E> {
    Ok(T),
    Err(E), 
}
```

## 泛型结构体
```rust
struct Point<T> {
    x: T,
    y: T, 
}

impl<T> Point<T> // 在 impl 代码段开头声明泛型
where
    T: std::ops::Add<Output = T> + std::ops::Mul<Output = T> + Copy,
{
    fn distance_from_origin(&self) -> T {
        self.x * self.x + self.y * self.y
    }
}
```

## Option 和 Result 类型
Rust 提供了两个非常方便的枚举类型：`Option` 和 `Result`。
```rust
enum Option<T> { 
    None,
    Some(T), 
}

enum Result<T,E> {
    Ok(T),
    Err(E),
}
```
合理地运用这两个枚举类型，可以便捷地传递参数、捕获错误。

```rust
fn eat(food: Option<String>) {
    match food {
        Some(c) => println!("Nice {}! I'm full.",c),
        None => println!("Nothing to eat. I'm hungry!"),
    }
}

fn divide(a: i32, b: i32) -> Result<i32, String>{
    if b == 0 {
        return Err(String::from("Division by zero"));
    }

    Ok(a / b)
}
```

!!! note "Option 与 Result 的数据获取"
    Rust 为 `Option` 和 `Result` 类型提供了 `unwrap()` 和 `expect()` 等方法进行快速的数据获取，可以绕开冗余的模式匹配。但程序员需要自己确保 `Option` 对应的类型为 `Some(_)`（`Result` 为 `Ok(_)`），否则会导致程序`panic`退出。

    ```rust
    fn main() {
         let a:Option<String> = Some("hello".to_string());
        let b: Result<_,&str> = Err("world");
        let content = a.unwrap() + " " + b.expect("Empty content"); // If a is None or b is Err, this will panic.
        // a is None: thread 'main' panicked at ...:
        // called `Option::unwrap()` on a `None` value
        // b is Err:thread 'main' panicked at ...:
        // Empty content: "world"
        println!("{}",content);
    }
    ```

