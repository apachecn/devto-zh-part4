# 提高防锈和网状装配性能🚀🚀🚀

> 原文：<https://dev.to/sendilkumarn/increase-rust-and-webassembly-performance-382h>

在浏览器中运行本机代码的梦想并不是什么新鲜事。有许多失败的尝试。他们都给了我们一个教训。这些知识使今天成为可能。

WebAssembly 使得在浏览器中运行 C、C++、Rust 等语言成为可能。

* * *

查看我关于 Rust 和 WebAssembly 的书[这里](https://sendilkumarn.com/wasm-book)

* * *

但是什么是 WebAssembly 呢？点击此处或[查看这篇来自林克拉克的](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)精彩帖子。

## TL；博士:

*   Rust 的工具链使编写 WebAssembly 应用程序变得容易。
*   如果你想要更好的性能，那么使用`opt-level=3`。
*   如果你想要一个更小的包，那么使用`opt-level="s"`。

# 我们该怎么办？

创建一个 WebAssembly 应用程序，它接受一个`markdown`格式的字符串并将其转换成 HTML。

# 我们开始吧

到目前为止，Rust 拥有 WebAssembly 的最佳工具。它与语言结合得很好。这使得 Rust 成为做 WebAssembly 的最佳选择。

我们需要在开始之前安装 Rust。要安装 Rust checkout，请查看此处的安装指南。

一旦你安装了铁锈。让我们开始创建应用程序。

# 创建应用程序

用所有必要的工具链创建一个 WebAssembly 应用程序:

```
npm init rust-webpack markdown-rust 
```

Enter fullscreen mode Exit fullscreen mode

这用 Webpack 创建了一个新的基于 Rust + JavaScript 的应用程序。

进入目录

```
cd markdown-rust 
```

Enter fullscreen mode Exit fullscreen mode

它既有`Cargo.toml`又有`package.json`。

Rust 源文件位于`src`目录中，JavaScript 文件位于`js`目录中。我们还配置了 webpack，以便轻松快速地运行应用程序。

`Cargo.toml`包含以下内容:

```
[package]
# Some package information. 
```

Enter fullscreen mode Exit fullscreen mode

然后，它声明项目将使用下面的命令构建一个`dynamic library`。

```
[lib]
crate-type = ["cdylib"] 
```

Enter fullscreen mode Exit fullscreen mode

我们还声明了发布概要文件应该使用`lto`标志来优化发布。

```
[profile.release]
lto = true 
```

Enter fullscreen mode Exit fullscreen mode

最后加了一些`[features]`和`[depdencies]`。

现在我们要做的就是为 Rust 添加`markdown`库，它将 Markdown (string)编译成 HTML 字符串。

```
[dependencies]
# some comments ......
wasm-bindgen = "0.2.45"
comrak = "0.6" 
```

Enter fullscreen mode Exit fullscreen mode

删除`src/lib.rs`中的所有内容，并替换为以下内容。

加载我们将要使用的`comrak`函数和`wasm_bindgen`。

```
use comrak::{markdown_to_html, ComrakOptions};
use wasm_bindgen::prelude::*; 
```

Enter fullscreen mode Exit fullscreen mode

#### 那么什么是`wasm_bindgen`？

WebAssembly 没有任何绑定来调用 JavaScript 或文档 API。事实上，我们只能在 JavaScript 和 WebAssembly 之间传递数字。但这并不总是令人满意的，我们需要在它们之间传递 JS 对象、字符串、类、闭包和其他东西。

> 我们如何实现这一目标？

我们可以创建一个绑定文件或胶水文件，帮助将上述对象转换为数字。例如，在字符串的情况下，而不是将每个字符作为字符代码发送。

我们可以将该字符串放在线性内存数组中，然后将起始索引(它在内存中的位置)及其长度传递给另一个世界(或 JavaScript)。另一个世界应该可以访问这个线性内存数组，并从那里获取信息。

但是对 JavaScript 和 WebAssembly 之间传递的每个值都这样做既耗时又容易出错。 [wasm_bindgen](https://rustwasm.github.io/wasm-bindgen/) 工具帮助您自动构建绑定文件，并删除带有单个`#[wasm_bindgen]`注释的样板代码。

但是我们需要非常小心有多少次跨越了 JavaScript 和 WebAssembly 模块之间的界限。我们穿越得越多，性能就越慢。

现在我们将创建一个名为 parse 的函数，它实际上接受 markdown 输入并返回 HTML。

```
#[wasm_bindgen]
pub fn parse(input: &str) -> String {
    markdown_to_html(&input.to_string(), &ComrakOptions::default())
} 
```

Enter fullscreen mode Exit fullscreen mode

`#[wasm_bindgen]`注释完成了将字符串转换成两个数字的所有样板工作，一个是指向线性内存中字符串开头的指针，另一个是字符串的长度。`#[wasm_bindgen]`也用 JavaScript 生成绑定文件。

#### JavaScript❤️的时间到了

现在我们已经准备好了 WebAssembly 模块。是时候使用一些 JavaScript 了。

我们将删除`js/index.js`中的所有行，并替换为以下内容。

我们将首先导入生成的 WebAssembly 模块。因为我们使用 Webpack，所以 Webpack 将负责引导`wasm_pack`，引导将依次使用`wasm_bindgen`将 Rust 转换为 WebAssembly 模块，然后生成必要的绑定文件。

`wasm_pack`是一个帮助构建和打包 Rust 和 WebAssembly 应用程序的工具。更多关于 Wasm-pack [这里](https://rustwasm.github.io/wasm-pack/)。

这意味着我们只需导入`pkg/index.js`文件。wasm_pack 将在这里生成输出。

```
const rust = import('../pkg/index.js'); 
```

Enter fullscreen mode Exit fullscreen mode

动态导入将创建承诺，该承诺在解析时给出 WebAssembly 模块的结果。我们可以像下面这样调用 Rust 文件内部定义的函数`parse`。

```
rust.then(module => {
    console.log(module.parse('#some markdown content')); 
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们还将计算使用 WebAssembly 模块解析内容所花费的时间。

```
rust.then(module => {
    console.log(module.parse('#some markdown content'));

    const startWasm = performance.now();
    module.parse('#Heading 1');
    const endWasm = performance.now();

    console.log(`It took ${endWasm - startWasm} to do this in WebAssembly`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了比较，我们还将计算用 JavaScript 做这件事所花的时间。

安装 JavaScript 的 markdown 库。

```
npm install --save marked 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，让我们编写 JavaScript 代码，它接收一个 Markdown 文本并返回 HTML。

```
 // js/index.js
import marked from 'marked';
// some content goes here;

const markdown = '#Heading';

const startJs = performance.now();
console.log(marked(markdown));
const endJs = performance.now();

console.log(`It took ${endJs - startJs} to do this in JavaScript`); 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用`npm run start`运行应用程序。这将启动 Webpack dev 服务器并从本地提供内容。

[https://codesandbox.io/embed/xenodochial-tesla-rimzu](https://codesandbox.io/embed/xenodochial-tesla-rimzu)

这是一个非常有趣的性能统计数据。

在 Chrome 和 Safari 中，JavaScript 的性能要比 WebAssembly 好得多。但是在 Firefox 中，JavaScript 版本比 WebAssembly 慢了 50%。

这主要是因为与其他浏览器相比，Firefox 中的 WebAssembly 链接和引导非常非常快。

如果看一下包的大小，WebAssembly 文件比 JavaScript 变体 1009 KB 大 7475 KB。

如果你现在对 WebAssembly 起哄，那就等着吧。

我们还没有添加任何优化。让我们添加一些优化并检查性能。

打开`Cargo.toml`文件，并在`[features]`部分上方添加以下段。

```
[profile.dev]
lto = true
opt-level = 3 
```

Enter fullscreen mode Exit fullscreen mode

`opt-level`只不过是编译项目的优化级别。

这里的`lto`指的是`link-time-optimization`。

> 注意:在处理实际应用程序时，这个优化级别和 lto 应该添加到`profile.release`中。

此外，启用`wee_alloc`,它分配的内存要少得多。

取消`Cargo.toml`
中的以下注释

```
[features]
default = ["wee_alloc"] 
```

Enter fullscreen mode Exit fullscreen mode

在`src/lib.rs`文件中添加`wee_alloc`内存分配。

```
#[cfg(feature = "wee_alloc")]
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们重新启动服务器。

[https://codesandbox.io/embed/hidden-sun-cdhms](https://codesandbox.io/embed/hidden-sun-cdhms)

我们现在可以看到 WebAssembly 的真正性能优势。在 Chrome 中，WebAssembly 版本比 JavaScript 版本快 4 倍。

在 Safari 中，JavaScript 变体仍然在 2-3 毫秒之间，但是 WebAssembly 变体在 0-2 毫秒之间。

Firefox 使用优化后的 WebAssembly 代码也比没有优化时快了近 50%。

现在，对于 WebAssembly 来说最重要的包大小是 1280 KB，对于 JavaScript 来说是 1009 KB。

我们也可以要求 Rust 编译器针对大小而不是速度进行优化。指定将`opt-level`改为`s`

```
opt-level = "s" 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/beautiful-clarke-cr649](https://codesandbox.io/embed/beautiful-clarke-cr649)

WebAssembly 仍然是明显的赢家，但 Chrome 注册的 WebAssembly 时间略有增加，但仍低于 JavaScript 变体。Safari 和 Firefox 都为 WebAssembly 提供了更高的性能。

对于 WebAssembly，包的大小进一步减小，大约为 1220 KB，对于 JavaScript，为 1009 KB。

Rust 编译器还支持进一步减小文件大小的`opt-level = "z"`。

```
opt-level = "z" 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/upbeat-ritchie-43rth](https://codesandbox.io/embed/upbeat-ritchie-43rth)

对于 WebAssembly，包的大小进一步减小，大约为 1161KB，对于 JavaScript 为 1009 KB。

Chrome 中的 WebAssembly 模块在 41 到 140 ms 之间使用`opt-level='z'`时性能波动很大。

IE Canary for Mac 的性能(~)几乎和 Chrome 一样。

如果你更关心你的包的大小，但是现在 v8 的性能不可靠，使用`opt-level="z"`。

我希望这能给你一个动力，开始你的 web 组装之旅。如果您有任何问题/建议/觉得我错过了什么，请随时添加评论。

* * *

如果你喜欢这篇文章，那么你可能会喜欢我关于 Rust 和 WebAssembly 的书。点击这里查看

* * *

你可以在推特上关注我。

如果你喜欢这篇文章，请留下赞或评论。❤️