# Rust 的结构体

## 结构体类型
!!! note "与 C++ 的对照"

    C++ 的 `struct` 本质上是默认 `public` 的 `class`，因此我们接下来仅讨论 Rust `struct` 和 C++ `class` 的区别。

Rust 中没有“类”的概念，但是保留了结构体。

```rust
struct Person {
    age: u32,
    name: String
}

let alice = Person {
    age: 20,
    name: String::from("Alice")
}
```

## 结构体方法
结构体可以有方法和关联函数，在 `impl` 块中实现。包含 `self`、`&self` 或 `&mut self` 的函数为方法，由 `结构体.方法名()` 调用；不包含的为关联函数，用 `结构体名::关联函数名()` 的方式调用。

```rust
impl Person {
    fn new(age: u32, name: String) -> Self {
        Self {
            age: age,
            name: name
        }
    }
    fn hello(&self) {
        println!("Hello! My name is {}.", name);
    }
}

let bob = Person::new(19, String::from("Bob"));
bob.hello();
```

执行以上代码将输出：

```text
Hello! My name is Bob.
```

!!! note "与 C++ 的对照"

    上文所述的方法和关联函数分别类似 C++ 中的方法和类方法（即在类中由 `static` 修饰的函数，不关联实例对象而关联类本身）。

    在构造和析构方面，由于 C++ 采用 [RAII](https://zh.wikipedia.org/wiki/RAII) 进行内存管理，每一个类都包含一个或多个构造函数以及一个析构函数。Rust 没有构造函数，但我们常用一个关联函数来进行结构体对象的构建（如上文的 `Person::new`）。Rust 采用 `Drop` trait 来管理对象的销毁，关于 trait 的讨论将在后续展开。 

