# Rust 快速入门

## Rust 环境安装

### 开发环境

你可以从选择以下任意一条路径，搭建 Rust 开发环境。

- Visual Studio + Rust Analyzer: 需要在本地安装好 `rustup`，Rust Analyzer插件能够在未编译的阶段提供代码报错与补全。

- JetBrains RustRover: JetBrains 提供现成的 Rust 编程环境，内置 Cargo，提供便捷的代码补全与编译运行。

- [Rust Playground](https://play.rust-lang.org/): 在线 Rust 编程环境。

### 编译工具链配置

#### 安装 rustup

在 Linux 或者 macOS 命令行环境下运行下述命令：

```bash
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

如果显示错误 `Commmand 'curl' not found`，运行下述命令：

```bash
$ sudo apt update
$ sudo apt install -y curl
```
安装完成后，重新运行上面的 rustup 安装命令。

如果网络正常，rustup 安装命令会提示下述信息：

```bash
Current installation options:


   default host triple: aarch64-apple-darwin
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
>
```

回车以后，它就会安装 Rust 所需要的编译器（rustc）、标准库（rust-std）等等。

!!! note "Windows 安装 rustup"

    前往 https://forge.rust-lang.org/infra/other-installation-methods.html，在 **Other ways to install rustup** 中，找到：

    - On Windows, download and run rustup-init.exe.

    下载并运行 **rustup-init.exe**。


#### 测试 rust 工具链

下载完成后，命令行会提示下述信息：

```bash
Rust is installed now. Great!

To get started you may need to restart your current shell.
This would reload your PATH environment variable to include
Cargo's bin directory ($HOME/.cargo/bin).

To configure your current shell, run:
source $HOME/.cargo/env
```

此时 shell 配置已更新。重启终端，PATH 环境变量就会包括 rustup 安装路径。如果想要在当前终端使用 rust 编译器，你也可以直接运行：

```bash
$ source $HOME/.cargo/env
```

完成后，可以通过下述检查 `rustc` 是否成功安装：

```bash
$ rustc --version
```

如果正常输出`rustc`版本信息，说明安装成功了。

## Hello World！

### 直接使用 rustc 编译

在当前目录下，通过下述命令创建项目。

```bash
$ touch hello.rs
```

在 `hello.rs` 中，写入如下内容：

```rust
fn main() {
    println!("Hello world!");
}
```

使用 `rustc` 编译，运行 `hello` 可执行程序并得到下述结果。

```bash
$ rustc hello.rs
$ ./hello
Hello world!
```

### 使用 Cargo 包管理器

你也可以通过 Cargo 包管理器来创建一个新项目。

```bash
$ Cargo new hello
```

此时 Cargo 会在你的当前目录下生成一个 `hello` 文件夹，进入文件夹，通过 `tree` 可以查看项目结构。

```bash
$ cd hello
$ tree
.
├── Cargo.toml
└── src
    └── main.rs
```

其中 `Cargo.toml` 为项目的配置文件，存放了项目的相关信息、依赖项等信息。

由 Cargo 生成的 `main.rs` 会默认生成上述 `Hello world!` 程序，直接编译运行即可。

```bash
$ cargo run
   Compiling hello v0.1.0 (/.../hello)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.46s
     Running `target/debug/hello`
Hello, world!
```

## Rust 项目结构

Rust 采用**模块化**的项目结构，每个文件/文件夹都被视为一个模块，文件内部还可以定义多个/多重模块。

可以通过 `use` 来访问模块。

```rust
// Consume that your src directory has main.rs and calendar.rs
mod calendar;
use calendar::get_day;

mod body {
    pub mod mouth {
        pub fn use_mouth() {}
    }
}

mod language {
    pub mod Chinese{
        pub fn speak() {
            use crate::body;
            body::mouth::use_mouth();
        }
    }
}

fn main() {
    get_day();
    language::Chinese::speak();
}
```

Rust 以**包（packages）**为项目单位，用户可以通过包构建、测试、分享**箱（crates）**（也成单元包，对应一个库或可执行文件）。用户也可以实现自己的箱，通过在 `Cargo.toml` 的 `[dependencies]` 中配置箱的对应路径，即可在程序中通过 `use` 使用自己的箱。

## Cargo 包管理器

Cargo 是 Rust 的一个非常便捷好用的**包管理器（*package manager*）**，许多 Rust 程序员都在使用 Cargo 来管理他们的 Rust 项目。Cargo 能够为你处理很多任务，比如**构建代码**、**下载代码所依赖的库**，以及**构建这些库**。（代码所需要的库通常被称为**依赖项**）

### 创建项目

```bash
$ cargo new <your-project-name>
```

### 编译项目

```bash
$ cargo run # 编译并运行项目
$ cargo build # 仅编译，不运行
$ cargo check #不编译，检查错误
```

如果希望发布项目，请使用 `cargo build --release` 对其进行项目优化。（此命令将在 target/release 而不是 target/debug 中创建可执行文件）

### 添加依赖

```bash
$ cargo add <crate>
```

通过该命令添加的依赖将会同步至 `Cargo.toml`中。

也可以直接在 `Cargo.toml` 中写入依赖，随后通过 `cargo check` 或 `cargo build` 直接构建。

### 自动格式化

```bash
$ cargo fmt
```

Cargo 提供了代码风格的自动统一，通过上述指令可快速实现缩进和换行的自动对齐。

### 运行测试

Cargo 可以通过 `#[test]` 标注测试函数，通过 `cargo test` 命令进行单元测试。

```rust
fn multiple(x: i32, y: i32) -> i32 {
        x * y
}

#[test]
fn multiple_test() {
        assert_eq!(2, multiple(1, 2));
}

fn main() {}
```

运行 `cargo test` 并查看结果。

```bash
$ cargo test
   Compiling Test v0.1.0 (/.../my_project)
    Finished `test` profile [unoptimized + debuginfo] target(s) in 0.45s
     Running unittests src/main.rs (target/debug/deps/my_project-5ab9f918a19e3cb1)

running 1 test
test multiple_test ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

可以通过 `cargo test -- <test-name> --test-threads=1` 来指定测试和同时运行的测试数。

!!! note "Tips"
    通过 `#[cfg(test)]` 可以实现测试代码仅在测试模式下编译。

更多 Cargo 的使用方法，请参考 Cargo 的[官方文档](https://doc.rust-lang.org/cargo/)

第三方包可以通过该链接查阅：https://crates.io
