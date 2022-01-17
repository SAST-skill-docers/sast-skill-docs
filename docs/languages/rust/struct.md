# Rust 的结构体与特性

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

对于“不同类共享相同的行为”这样的要求，Rust 使用一个叫做 `trait` 的机制来实现。

```rust
struct Cat {}

struct Dog {}

trait CanMakeSound {
    fn make_sound(&self) -> String;
}
```

上文定义了一个叫做 `CanMakeSound` 的 `trait`，在其中声明了 `make_sound` 方法而没有实现。如果要为一个类型实现 `trait`，则需要实现该 `trait` 的所有方法。实现 `trait` 的语法形如 `impl 特性名 for 类型名`。

```rust
impl CanMakeSound for Cat {
    fn make_sound(&self) -> String {
        String::from("Meow")
    }
}

impl CanMakeSound for Dog {
    fn make_sound(&self) -> String {
        String::from("Woof")
    }
}
```

在一个类型实现该 `trait` 后，这个类型的变量就可以使用其方法。

```rust
let cat = Cat {};
let dog = Dog {};

println!("{}\n{}", cat.make_sound(), dog.make_sound());
```

!!! note "与 C++ 的对照"

    在 C++ 中，不同类要想共享行为，则需要声明一个基类，将希望共享的行为声明为基类中的虚函数，然后分别继承该基类并重载希望共享的函数。例如，对上文的 `Cat` 和 `Dog`，在 C++ 中会这样实现：

    ```C++
    class CanMakeSound {
        virtual std::string make_sound() = 0;
    };
    class Cat : public CanMakeSound {
        std::string make_sound() override {
            return "Meow";
        }
    };
    class Dog : public CanMakeSound {
        std::string make_sound() override {
            return "Woof";
        }
    };
    ```

如果仅仅是直接进行方法调用，那么使用或不使用 `trait` 看起来并无太大区别。但 `trait` 还可以用于函数参数的声明，这样就可以对参数类型加以限定而不需要完全确定类型：

```rust
fn animal_make_sound(animal: impl CanMakeSound) -> String {
    animal.make_sound()
}
```

事实上，这是 `Trait Bound` 语法的一个语法糖。如果展开写则形式如下：

```rust
fn animal_make_sound<T: CanMakeSound>(animal: T) -> String {
    animal.make_sound()
}
```

`Trait Bound` 也可以不仅包含一个 `trait`

```rust
fn some_function<T: Display + Clone, U: Clone + Debug>(t: T, u: U) -> i32;
// 此处的 Display、Clone 和 Debug 都是 rust 语言提供的 trait
```

也可以采用 `where` 从句来简化过长的 `Trait Bound`

```rust
fn some_function<T, U>(t: T, u: U) -> i32
    where T: Display + Clone, U: Clone + Debug;
```