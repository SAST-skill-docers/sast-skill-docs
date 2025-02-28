# Rust 的特型

Rust 具有与 C++ 完全不同的面向对象，Rust 中没有继承的机制，对于“不同类共享相同的行为”这样的要求，Rust 使用一个叫做 `trait` 的机制来实现。

!!! note "组合优先于继承"
    Rust 特型机制的核心思想在于**“组合优先于继承”**，这种设计理念使得相比较于 C++ 更关注于抽象的类的继承关系，Rust 更关注每个对象的特性，通过特性的组合来定义一个完整的对象。
    
    比如，我们想要定义一个鸟类，我们在鸟类中实现了非常多的特性。这个时候我们想把企鹅也加入到我们的系统中，企鹅也是鸟，但是企鹅不会飞。那这怎么解决呢？我们不得不重构我们的代码，比如再重新定义一个会飞的鸟和不会飞的鸟，然后共同继承自抽象鸟。然后我们还得把要求输入参数为鸟的函数都给按照实际情况改成抽象鸟或者会飞的鸟。过了一会，蝙蝠又要加入。蝙蝠在飞行方面和鸟很像，但确是哺乳动物，继续重构。不知不觉中我们的代码也越来越抽象了。
    
    那如果用组合该如何解决这个问题呢？我们不必把所有鸟类共性都放到鸟类里面，而是可以把每一个共性单独定义成一个Trait，然后我想要能飞能吃饭且生物学上是鸟类的生物的时候可以把参数约束成Fly+Eat+BiologicallyBird ，只想要飞而不关心是不是鸟的时候可以约束成Fly。这样我们就可以很方便地组合出我们想要的生物了。
    
    （参考自[zhc7](bjhansen2012@outlook.com)的2024暑培讲义）

Rust 的 `trait` 机制通过如下代码实现：

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
`trait` 可以拥有默认实现。

```rust
pub trait Fly {
    fn fly(&self) {
        println!("Taking off!");
    }
}
```

也可以实现多重 `trait`。

```rust
pub trait HaveWings() {
    fn flap(&self);
}

pub trait HaveLegs() {
    fn walk(&self);
}

pub trait CanFly(): HaveWings + HaveLegs {
    fn fly(&self);
    fn land(&self);
}
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

!!! note "Rust 提供的 traits"
    事实上，Rust 的标准库 `std` 提供了非常多好用的 `trait`，你可以直接通过 `#[derive()]` 的方式，快捷地为你的结构体实现指定 `trait`。
    这里给出一些常用的 trait：
    
    `Debug`: 可以用 `println!("{:?}", my_struct)` 来打印结构体的调试信息。

    `Clone`: 可以使用 `my_struct.clone()` 创建结构体的副本。

    `PartialEq`: 可以使用 `==` 和 `!=` 进行结构体对象之间的部分相等性比较。如果要实现完全相等性比较，可以使用 `Eq`。

    `PartialOrd`: 可以使用 `<`、`<=`、`>`、`>=` 进行比较。

    更多 `trait` 可以参考 [Rust std 官方文档](https://doc.rust-lang.org/std/index.html)