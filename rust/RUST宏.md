
## RUST使用

1. 方法顶部 **#[no_mangle]** 属性用于告诉编译器不要对函数或静态变量进行名称重整。
2. 当我们需要在 Rust 中定义的函数或变量能够被其他语言的代码直接调用，比如 C 或 C++，wasm。在这种情况下，我们需要禁止 Rust 编译器对函数名进行重整，以便其他语言能够正确地引用它们（wasm中使用）
3. **#** 属性符号，用于属性标记的开始，后面紧跟着方括号 **[]** 来包裹属性的名称和可能的参数。_**用于提供附加的信息或指令给编译器或其他工具。**_

这些属性符号可以在 Rust 代码中使用，以_**控制编译器的行为、修改代码的生成方式，或者提供其他与代码本身无关的信息**_。常见的用途包括禁止名称重整、自动实现 trait、控制编译器的警告行为、启用实验性功能等。

- **#[derive]** 用于自动实现特定的 trait，Trait 是 Rust 中一种定义共享行为的方法，通过实现 trait，可以为类型提供额外的功能

1. **Clone**：用于创建类型的副本。
2. **Copy**：用于在栈上进行复制的类型。
3. **Debug**：用于格式化输出类型的调试信息。
4. **Default**：用于创建类型的默认值。
5. **Eq** 和 **PartialEq**：用于比较相等性。
6. **Hash**：用于哈希类型。
7. **Ord** 和 **PartialOrd**：用于比较大小。

简单使用：

```
#[derive(Clone, Copy)]
struct Point {
    x: i32,
    y: i32,
}


//上面结构体就可以clone了

let p1 = Point { x: 1, y: 2 };
let p2 = p1.clone();
```

**#[wasm_bindgen]** 宏的作用是将 Rust 代码编译成可以直接在 Web 环境中使用的 Wasm 模块，并提供与 JavaScript 代码交互的能力。

#### **#[cfg] 定义编译条件** 是 "configuration" 的缩写。

允许在编译代码时根据特定条件选择性地包含或排除代码块

通常情况下，**#[cfg]** 属性用于在不同的操作系统、架构或编译器版本下编译不同的代码。

以下是一些常见用法：

```
//指定编译平台
#[cfg(target_os = "linux")]
fn main() {
    println!("This code runs only on Linux!");
}
//指定特定版本
#[cfg(version = "1.51")]
fn main() {
    println!("This code runs only on Rust version 1.51!");
}
//自定义编译条件
#[cfg(my_custom_condition)]
fn main() {
    println!("This code runs only when 'my_custom_condition' is true!");
}
```

**#![feature]** 是 Rust 中用于启用实验性功能的属性

**#![warn]** 是 Rust 中用于开启编译器警告的属性

- cdylib 用来指明库的类型。用于创建一个动态链接库（Dynamic Link Library，DLL），以供其他语言调用。、
- 几种常见的 crate 类型：

- **bin**：生成可执行文件
- **rlib**：生成静态链接库，用于在 Rust 项目中静态链接其他 crate。这对于构建可重用的 Rust 库非常有用。
- **staticlib**：生成静态链接库，，但用于与其他语言进行静态链接。
- **proc-macro**：生成一个过程宏，用于在编译时转换 Rust 代码

wasm-bindgen 是一个简化 Rust WASM 与 JS 之间交互的库。

如果你修改了 **Cargo.toml** 文件中的 **dependencies** 部分，建议执行 **cargo build** 或 **cargo run** 命令以确保你的项目正确地更新了依赖项

二进制包：

- main.rs 是二进制项目的入口
- 二进制项目可直接执行
- 一个项目中二进制包可以有多个，所以在 Cargo.toml 中通过双方括号标识 [[bin]]

库包：

- lib.rs 是库包的入口。
- 库项目不可直接执行，通常用来作为一个模块被其他项目引用。
- 一个项目中库包仅有1个，在 Cargo.toml 中通过单方括号标识 [lib]

_**wasm-pack 一个打包工具**_  
  

RUST导入扩展：

```
use foo::*;

//* 不代表导入全部，而是用于通配符导入（glob import）
// 导入了模块中被标记为 pub 的。

//通配符导入通常不被推荐，因为它会将大量符号引入当前作用域，导致命名冲突和可读性降低。
```