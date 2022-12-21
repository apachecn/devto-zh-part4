# web assembly——有听起来那么可怕吗？

> 原文：<https://dev.to/jackyef/webassembly-is-it-as-scary-as-it-sounds-31g7>

### web assembly——有听起来那么恐怖吗？😱

[![](img/a369ebcd57e583e65bebd42ec7d49252.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2HVtHJrH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKoFqaWev5QQ_CjhzAojv4g.png)

**web assembly(*Wasm*)**已经有 2 年多了。它仍然是一项相对较新的技术。出于某种原因，它总让我感到害怕。也许是因为它的名字上写着“*？或者仅仅是想象不得不用一种与 JavaScript 截然不同的语言来编码已经让我感到恐惧了？我是否已经变得非常依赖 JavaScript 的怪癖和特性，以至于不使用 JavaScript 编写代码的想法看起来如此令人倒胃口？不管是什么，我的好奇心无法忍受不知道它。在看了今年 I/O 大会上苏尔马的演讲后，我终于下定决心学习 WebAssembly。*

 *<figure>[![](img/69b6f7a182833c12362712ce98dbbe03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jR4KezbP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3D7N8Qr7UE0BGj7D-8ppMQ.png)

<figcaption>JavaScript 开发者在尝试学习 C++时做出的表情。(图片摘自[苏尔马](https://medium.com/u/2d30b2439b4b)的谈话)</figcaption>

</figure>

### ***到底什么是 WebAssembly？为什么重要？***

> [WebAssembly]提供了一种在 web 上以接近本地速度运行用多种语言编写的代码的方式，客户端应用程序在 web 上运行，这在以前是不可能的。— [MDN](https://developer.mozilla.org/en-US/docs/WebAssembly)

基本上，WebAssembly 允许我们编译用 C、Rust 等语言编写的代码。([完整的语言列表可以在这里找到](https://github.com/appcypher/awesome-wasm-langs))到*。wasm* 文件，然后在浏览器上运行它。从为网络平台打开所有新的可能性的角度来看，这是不可思议的。如果你以前尝试过用 [Unity](https://unity.com/) 制作游戏，你可能会对这张图片很熟悉。

<figure>[![](img/1e6c0658a7d65a352fb5bc32d01db259.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f0S5mAux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3zDCsIwsZU6Tm7VmZT0oDg.png) 

<figcaption>团结游戏:坦克！</figcaption>

</figure>

这是一个用 unity 制作的 3D 游戏样本，在 WebAssembly 的帮助下移植到浏览器上！疯狂的是:玩起来一点都不滞！你可以自己试试[这里](https://wasm.bootcss.com/demo/Tanks/)。另一个故事是 AutoCAD。AutoCAD 也被移植到网络上。你可以观看[这个视频](https://youtu.be/BnYq7JapeDA?t=1612)来了解更多。WebAssembly 不是魔术。它不会自动获取用 C++编写的现有程序，然后编译它，它会在浏览器中运行。WebAssembly 允许我们重用其他语言的代码，并以接近本地速度在浏览器上运行它！

#### 我能看到它的好处，但是我需要学习它吗？

如果你的 web 应用不做繁重的计算，而只做一些简单的任务，比如渲染 UI，请求 API 等等。那么你很可能不需要 WebAssembly。WebAssembly 允许我们作为 web 开发人员实现以前仅用 JavaScript 无法(或至少不可行)实现的事情。它是 JavaScript 的补充，而不是替代。话虽如此，虽然你可能不会使用它，但学会它并尝试它总是有益的。

### 前言

在本文中，我们将使用 wasm 从图像输入中解码 QR 码。我们将使用 Rust 和 [wasm-bindgen](https://rustwasm.github.io/docs/wasm-bindgen/) 来帮助我们做到这一点。最终结果锈*箱*可以在下面的回购中看到。

jackyef/rq RR-wam

请注意，我绝不是网络组装和/或生锈的专家。我在软件工程方面的经验主要包括网络开发和编写 JavaScript。这基本上意味着我在本文中所做的可能不是一个好的实践，并且其中一些可能在技术上不准确；但对于一个刚接触这种环境的人来说，这是一次真正的学习之旅。希望你会发现这在某种程度上是有用的！

### **设置**

首先，我们需要安装 Rust。只要按照[这个指南](https://doc.rust-lang.org/book/ch01-01-installation.html)去做就行了。一旦完成，我们将把 wasm 作为编译目标添加到 Rust 编译器中。我们通过运行以下命令来实现。

```
rustup target add wasm32-unknown-unknown 
```

现在，我们应该安装`wasm-bindgen-cli`，因为我们以后会用到它。我们将使用货物安装它。应该已经和铁锈一起安装了。你可以把它想象成 npm，但是对于 Rust 来说。运行以下命令安装 wasm-bindgen-cli。

```
cargo install wasm-bindgen-cli 
```

这就是我们现在需要的！在我们开始之前，让我给你一个我们将要做的概述。

1.  用 Rust 写一个可以解码二维码的函数
2.  将 Rust 代码编译成 wasm
3.  尝试在简单的 HTML + JS 网页中使用 wasm 文件🎉

### 在铁锈中编写代码

如果你熟悉 Node，通常我们通过运行`npm init`来启动一个项目。在 Rust 中，我们用`cargo new hello_world`来代替。这将创建一个名为 hello_world 的目录，其中包含一些为我们预先制作的文件。在 Rust 中，这个项目被称为包。我们也可以导入第三方*板条箱*，就像在 Node 中我们可以导入第三方模块一样。现在，让我们看看`Cargo.toml`文件。你的可能现在看起来有点空，但没关系。只需修改它以遵循下面的代码片段。*