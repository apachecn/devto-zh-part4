# 小锈入门提示系列:模块和导入

> 原文：<https://dev.to/5422m4n/little-rust-starter-hint-series-modules-and-imports-974>

这篇文章是对来自[的 Ralf Jung](https://github.com/RalfJung) 的在线书籍 rust101 的补充。以防事情有点不清楚，建议你另外读一读那本书/那一节，这样就会明白了。

今天我想向大家展示如何将你在 rust 2018 中的代码分成几个文件，并从这里和那里使用片段。

让我们假设您有一个由`cargo`创建的普通 rust 项目，看起来像:

```
tree . -L 2                                                                                                                                                              .
├── Cargo.lock
├── Cargo.toml
├── src
│   ├── main.rs
│   ├── part01.rs
│   ├── part02.rs
│   ├── part03.rs
│   ├── part04.rs
│   ├── part05.rs
│   └── part06.rs 
```

Enter fullscreen mode Exit fullscreen mode

所以你得到了那个`main.rs`和几个其他的 rust 文件。这些文件可以被称为模块。

但是，为了能够使用例如在 [`part05.rs`](https://www.ralfj.de/projects/rust-101/part05.html) 中定义的来自不同模块如`part06.rs`的函数或数据结构，必须显式声明整个结构。

## 简单易行的方法

在你的`main.rs`中，你可以声明你已经得到的所有模块:

```
// main.rs

mod part01;
mod part02;
mod part03;
mod part04;
mod part05;
mod part06;

pub fn main() {
    part06::main();
} 
```

Enter fullscreen mode Exit fullscreen mode

以便您可以使用例如在`part06.rs`
中的模块`part05.rs`中定义的数据结构

```
// part06.rs

use crate::part05::BigInt;      // <-- uses absolute imports
// or 
// use super::part05::BigInt;   // <-- uses relative imports 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`BigInt`必须用关键字`pub`
声明为公共结构

```
// part05.rs

pub struct BigInt {
  pub data: Vec<u64>, // least significant digit first, no trailing zeros
} 
```

Enter fullscreen mode Exit fullscreen mode

## 敬酒不吃吃罚酒

现在让我们假设在你的`main.rs`中，你没有关于你的模块`part05.rs` :
的明确定义

```
// main.rs

mod part06;

pub fn main() {
    part06::main();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到:

```
// part06.rs

use crate::part05::BigInt;

/* leads to the following error:

unresolved import `crate::part05`
part06.rs(2, 12): maybe a missing `extern crate part05;`?

 */ 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，模块`part05`并不像我们预期的那样广为人知。那么我们如何在不升级`main.rs`中的模块的情况下解决这个问题呢，让我们来看看`#[path]`指令:

```
// part06.rs

#[path = "part05.rs"]
mod part05;
use part05::BigInt; 
```

Enter fullscreen mode Exit fullscreen mode

`#[path]`指令告诉编译器使用文件`part05.rs`作为名为 part05 的模块。以便我们现在可以将它用作本地导入。

注意，不再需要前缀`crate::`,因为我们在这里只在局部范围内声明了模块。

# 进一步阅读

在文档中也有[进一步的阅读，将详细阐述 rust 版本 1.30 中引入的模块的新约定。](https://doc.rust-lang.org/reference/items/modules.html)

使用的版本:

```
$ rustc --version
rustc 1.37.0 (eae3437df 2019-08-13)
$ cargo --version
cargo 1.37.0 (9edd08916 2019-08-02) 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读，不要错过反馈:)