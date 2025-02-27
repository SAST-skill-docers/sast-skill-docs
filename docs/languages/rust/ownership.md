# Rust 中的所有权

为了兼顾内存使用的**安全**和**性能**，Rust 在设计之初就采用了与 C++ 完全不同的内存管理。

Rust 引入了**所有权（ownership）**的概念：
- Rust 中的每个值都有所有者 (owner)。
- 同一时刻每个值只有一个所有者。
- 当所有者失效，值也将被丢弃。

变量通过绑定的方式，获得对数据的所有权。如果一个绑定超出作用域，其绑定的数据将被自动释放

```rust
let a = 1;
```

变量之间的**赋值**行为，将会直接导致绑定关系的变化，这一语义被称为**移动所有权**，亦即**掩盖 (shadowing)**。

```rust
let s1 = String::from("hello");
let s2 = s1; // 字符串 "hello" 已经被 s2 绑定，s1 悬置。

println!("{s1}, world!");
// 此时将发生编译错误 error: borrow of moved value: `s1`
```

但并非所有语境下的变量赋值都希望移交所有权，这个时候应该采用 Rust 中的**借用（borrow）**概念。

```rust
let v = vec![1, 2, 3];
let v_ref = &v; // v_ref is a reference to v.
```

!!! note "移动所有权"
    - 移动所有权是编译时的语义，不涉及程序运行时的数据移动，数据对应的内存区域并没有改变，只是更改了对应的变量名（类似C++的指针）。
    - 移动是默认行为(通过绑定或赋值)，不需要像 C++ 那样用 std::move 来显式指定。
    - 原来的变量依然拥有对数据的所有权。


当借用发生时，会对原来的变量增加限制：
- 当一个变量有引用存在时，不能移交它所绑定的数据的所有权。

```rust
let v = vec![1, 2, 3];
let v_ref = &v; 
let v_new = v;
// Moving ownership to v_new would invalidate v_ref.
// error: cannot move out of `v` because it is borrowed 
```

Rust 类型系统中，变量被分为**可变**与**不可变**。在借用中，同理可分为**可变借用** `&mut` 和**不可变借用** `&`。
```rust
fn main() {
    let mut vector: Vec<i32> = vec![];
    let vector_ref: &mut Vec<i32> = &mut vector; 
    push(vector_ref, 4);
}
```

!!! note "借用规则"
    - 不能在某个对象不存在后继续保留对它的引用。一个对象可以
      - 同时存在**多个不可变**引用(&T)
      - 或者**仅有一个可变引用**(&mut T)。 
    - 以上两者不能同时存在

需要注意的是，**函数**的**参数传递**时，采用的是与变量赋值和借用相同的语法规则。如果不指定 `&` 或 `&mut`,变量会直接发生移动。
```rust
fn hello_move(s: String) {
    println!("Message by moving: {}", S);
}

fn hello_borrow(s: &String) {
    println!("Message by borrowing: {}", S);
}

fn main() {
    let x: String  = String::from("hello");
    let y: String  = String::from("hello");
    
    hello_move(x);
    hello_borrow(&y);
    
    println!("x:{}",x); // error[E0382]: borrow of moved value: `x`
    println!("y:{}",y); // This runs normally. 
}

```

!!! note "变量的拷贝"
    如果想要直接获得一份变量的副本，可以使用 Rust 的 Copy 特型。

    - 大多数基本类型是 Copy 类型(i32、f64、char、bool 等等)。
      - 基本类型发生赋值时，会发生拷贝而非移动。
  
    
    ```rust
    let x: i32 = 12;
    let y = x; // `i32` is `Copy`, so it's not moved :D 
    println!("x still works: {}, and so does y: {}", x, y);
    ```
    
    - 可以通过 `impl Copy for ...` 或 `#[derive(Copy)]` 宏为变量实现 Copy 特型
      - 非基本类型在实现了 Copy 特型后，可以通过 `clone()` 等方法进行拷贝。
    
    ```rust
    let x: String  = String::from("hello");
    let y = x.clone(); // here copy an 'x' to y
    println!("x still works: {}, and so does y: {}", x, y);
    ```
所有权的思想贯穿了 Rust 编程的全过程，在后续的 **变量**、**函数**、**结构体** 等章节中，你还会多次遇到所有权的**转移**与**借用**问题。