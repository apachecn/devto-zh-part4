# 不要惊慌！`

> 原文：<https://dev.to/teckert/luka-04-don-t-panic-576o>

> 本文是使用 Rust/Wasm、Vue 和 TypeScript 开发逆向波兰符号计算器网络应用程序系列的一部分。你可以在 [GitHub Repo](https://github.com/t-eckert/luka) 上阅读更多信息并关注发展。

在 [Luka 03](https://dev.to/teckert/luka-03-see-assess-4p7d) 的最后，我不确定是使用 Wasm 连接 Rust 和 TypeScript，还是组织状态如何在 Vue 应用程序中传递。我决定先对付前者。我想我会碰到一些有趣的墙，这些墙可能会影响我如何构建 Vue 应用程序。

## 查找参考文献

我曾经在一个教程中连接过 Rust 和 JavaScript，但是我不太了解这个连接是如何工作的，以及如何正确设置它。这也是这个项目的部分动机——鞭策自己去学习。

以下是我正在通读的一些资源，以了解其结构:

*   [Rust Wasm 官网](https://rustwasm.github.io/)
*   [锈与 web 组装书](https://rustwasm.github.io/docs/book/)
*   [Rust 和 WebAssembly GitHub](https://github.com/rustwasm)
*   [Wasm Bindgen Book](https://rustwasm.github.io/docs/wasm-bindgen/)

我不认为我会需要`wasm-pack`，因为我正在将 Wasm 添加到一个现有的项目中，但是我还是下载了最新的版本(0.8.1)。我知道我将需要`wasm-bindgen`作为依赖。

```
[dependencies]
wasm-bindgen = "0.2.50" 
```

Enter fullscreen mode Exit fullscreen mode

我想我们有了一个好的开始。

我看到有人提到使用`wee_alloc`来最小化 Wasm 的生成量。我认为这听起来不错，但我还不会开始。

对于`lib.rs`，我正在添加`wasm-bindgen`装饰者的导入。所有的测试都通过了，这很好。事实证明，我确实需要`wasm-pack`来构建`.wasm`文件(可能是另一种方式，但这是本教程中显示的内容)。

构建`.wasm`的第一次尝试给出了一个很好的信息性错误消息。

```
Error: crate-type must be cdylib to compile to wasm32-unknown-unknown. Add the following to your Cargo.toml file:

[lib]
crate-type = ["cdylib", "rlib"] 
```

Enter fullscreen mode Exit fullscreen mode

好的。我把那个位加到了`cargo.toml`里。

它...奏效了。我的意思是，我没有修饰任何函数，所以它没有在 wasm 端生成任何函数，但仍然。哇哦。

现在来装饰一下功能。我不确定我是否必须装饰每个函数，或者我是否可以只装饰`struct`和`impl`...

它失败了，但是失败是因为`std::vec::Vec<f64>`没有实现`std::marker::Copy`。看看 Rust 文档，这对于所有的`Vec<T>`都是正确的，因为它管理一些其他的资源，正如它对`Drop`的实现所证明的。

我过不了一个`Vec<T>`？这很令人困惑，因为它们是如何在`Universe`结构[和](https://rustwasm.github.io/docs/book/game-of-life/implementing.html)中实现的呢？我肯定不明白这里的一些事情。

如果我真的不能传递一个`Vec<T>`，如果我让 TypeScript 管理整个堆栈状态，我真的只需要担心 Rust 代码中堆栈的最后两个值。可行的解决方案可能是这样的:

1.  TypeScript 持有状态`[4.5, 3, 99, 0.5]`。
2.  按下“+”按钮。
3.  堆栈中的最后两个值被传递给 Rust `(99, 0.5)`。
4.  生锈返回`(99.5)`。
5.  TypeScript 将堆栈更改为读取`[4.5, 3, 99.5]`。

这实际上似乎是一个更好的设计。它最大限度地减少了状态传递的数量，并给堆栈一个明确的所有者。

## 你试过在 Rust 里重写这个吗？

这个新实现中的所有东西都将是一个函数，而不是一个`State`结构。每个函数将接受两个数字并返回一个`Result`类型，该类型将给出接受运算的数字的结果或一个错误。对于操作(+、-、*、/)，将有一个`operate`函数接受两个数字和一个匹配其中一个操作的字符串，然后将状态返回给 TypeScript。

只有一个大问题。无论如何，这也是我在早期实现中会有的。

Rust 中的`+`、`-`和`*`函数都返回原始类型，就好像它们的输入类型是正确的一样，不存在不能返回原始数字的情况。然而，`/`有一个可怕的极端情况:被零除。如果你正在设计 Rust，你必须做出选择。要么是`/`函数是唯一返回`Result`或`Option`类型的函数，要么是如果试图被零除，而返回类型是符合人机工程学的其他函数的原始类型，那么该函数会出现混乱。

在前一种情况下，处理分工会很混乱:

```
//                                 Type
let a = 2                       // i32
let b = 0                       // i32

let added = 2 + 0               // i32
let subtracted = 2 - 0          // i32
let multiplied = 2 * 0          // i32
let divided = 2 / 0             // Err

match divided {
    Ok(quotient) => quotient,   // i32 (or some float maybe)
    Err(message) => message,    // String
} 
```

Enter fullscreen mode Exit fullscreen mode

我是说，什么？每次除法都要解商？我认为他们做出了正确的决定，如果你试图除以零，除法`panics!`。然而，我不知道这将如何跨越 Wasm 边界。这里有一个我*可以*解决的方法:

```
/// Perform the operation
#[wasm_bindgen]
pub fn operate(a: f64, b: f64, operation: String) -> Result<f64, String> {
    match &operation[..] {
        "+" => Ok(a + b),
        "-" => Ok(a - b),
        "*" => Ok(a * b),
        "/" => divide(a, b),
        _ => Err(format!(
            "Operation string `{}` does not match any allowed operation (+, -, *, /)",
            &operation[..]
        )),
    }
}

fn divide(a: f64, b: f64) -> Result<f64, String> {
    // Preemptively catch the division by zero panic case.
    // See documentation for [Div](https://doc.rust-lang.org/std/ops/trait.Div.html)
    match b {
        0.0 => Err(String::from("Division by zero is not ok.")),
        _ => Ok(a / b),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了在 Rust 的未来版本中匹配浮动是不可能的。这段代码，编译时给出错误:

```
warning: this was previously accepted by the compiler but is being phased out; it will become a hard error in a future release!
note: for more information, see issue #41620 <https://github.com/rust-lang/rust/issues/41620> 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

我不想写有朝一日行不通的代码，所以我也不打算写。相反，我需要弄清楚如何处理`panic!`并将`Err`传回给 Vue。现在，我在 Rust-lang 论坛上打开了[这个话题](https://users.rust-lang.org/t/how-should-i-handle-f64-division-by-zero-panic-elegantly/32485)，希望有人能好心帮助我。

开始提交:[DFA 42833 e 39 afbf 33 fc 202581 DC 0564321 dff 15 f](https://github.com/t-eckert/luka/tree/8da99f691e0b0bb50486b7bdeb348b1a8df8b1c4)

结束提交:[8da 99 f 691 e 0 b 0 bb 50486 b 7 bdeb 348 B1 A8 df 8 B1 C4](https://github.com/t-eckert/luka/tree/8da99f691e0b0bb50486b7bdeb348b1a8df8b1c4)