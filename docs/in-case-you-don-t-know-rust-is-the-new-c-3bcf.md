# 如果你不知道，锈是新的“C”

> 原文：<https://dev.to/yaser/in-case-you-don-t-know-rust-is-the-new-c-3bcf>

好书:[“Rust 是系统编程的未来，C 是新的汇编”:英特尔首席工程师 Josh Triplett](https://hub.packtpub.com/rust-is-the-future-of-systems-programming-c-is-the-new-assembly-intel-principal-engineer-josh-triplett/)

以前真的看不出来这个，但是 Rust 是 C/C++的替代品，干净的替代品。

这里是[氧化还原 OS](https://www.redox-os.org) :

> Redox 是一个用 Rust 编写的类似 Unix 的操作系统，旨在将 Rust 的创新引入现代微内核和全套应用程序。

[![redox](img/c142f33a4fe7802e9dc45df9fe2867cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d8o305EM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.redox-os.org/img/redox-orbital/large.webp)

Wasmer 在网络世界很有前途:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [瓦斯梅里奥](https://github.com/wasmerio) / [瓦斯默](https://github.com/wasmerio/wasmer)

### 🚀支持 WASI 和 Emscripten 的领先 WebAssembly 运行时

<article class="markdown-body entry-content container-lg" itemprop="text">[![Wasmer logo](img/61c28be5003d7e714f0b4deeb107145a.png)](https://wasmer.io) 

[![Build Status](img/d7f7c858edd55d888e2d58fed1357ffe.png)](https://github.com/wasmerio/wasmer/actions?query=workflow%3Abuild)[![License](img/19a3b2ab58cb62590ab6e9ff9a997359.png)](https://github.com/wasmerio/wasmer/blob/master/LICENSE)[![Wasmer Docs](img/98c751f8500ac11a26c348e5f8ea616d.png)](https://docs.wasmer.io)[![Slack channel](img/92eb5bc256b4b4a2ba9c29d5e8024643.png)T11】](https://slack.wasmer.io)

Wasmer 是一个*快速*和*安全*[T5】web assembly](https://webassembly.org)运行时，它使超级*轻量级容器*能够在任何地方运行:从*桌面*到*云*、*边缘*和*物联网*设备。

> *该文件在 [<g-emoji class="g-emoji" alias="cn" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f1e8-1f1f3.png">🇨🇳</g-emoji> 也有中 文-中文](https://github.com/wasmerio/wasmer/blob/master/docs/cn/README.md)[<g-emoji class="g-emoji" alias="de" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f1e9-1f1ea.png">🇩🇪</g-emoji>德语](https://github.com/wasmerio/wasmer/blob/master/docs/de/README.md)[<g-emoji class="g-emoji" alias="es" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f1ea-1f1f8.png">🇪🇸</g-emoji>西班牙语-西班牙语](https://github.com/wasmerio/wasmer/blob/master/docs/es/README.md)[<g-emoji class="g-emoji" alias="fr" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f1eb-1f1f7.png">🇫🇷</g-emoji>法语-法语](https://github.com/wasmerio/wasmer/blob/master/docs/fr/README.md)[<g-emoji class="g-emoji" alias="jp" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f1ef-1f1f5.png">🇯🇵</g-emoji>日本 語——日语](https://github.com/wasmerio/wasmer/blob/master/docs/ja/README.md)[<g-emoji class="g-emoji" alias="kr" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f1f0-1f1f7.png">🇰🇷</g-emoji>한국어——韩语](https://github.com/wasmerio/wasmer/blob/master/docs/ko/README.md)* 。

### 特征

*   默认安全。除非明确启用，否则没有文件、网络或环境访问权限。
*   支持 [WASI](https://github.com/WebAssembly/WASI) 和[编剧](https://emscripten.org/)开箱。
*   很快。以接近本机的速度运行 WebAssembly。
*   可嵌入到多种编程语言中
*   符合最新的 WebAssembly 建议(SIMD、引用类型、线程，...)

### 安装

wasmr cli 作为一个独立的可执行文件提供。

```
curl https://get.wasmer.io -sSfL | sh
```

Enter fullscreen mode Exit fullscreen mode<details><summary>Other installation options (Powershell, Brew, Cargo, ...)</summary>

Wasmer 可以从不同的软件包管理器安装。选择最适合您环境的产品:

*   Powershell (Windows)

    ```
    iwr https://win.wasmer.io -useb | iex
    ```

    Enter fullscreen mode Exit fullscreen mode
*   自制软件(macOS，Linux)

    …Enter fullscreen mode Exit fullscreen mode</details> </article>

[View on GitHub](https://github.com/wasmerio/wasmer)

甚至它可能接管像这样的当前大型 C/C++实现:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[rust python](https://github.com/RustPython)/[rust python](https://github.com/RustPython/RustPython)

### 用 Rust 写的 Python 解释器

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/adbf740ee8acbb3ac7443b6d22179b9c.png)](https://github.com/RustPython/RustPython./logo.png)

# [RustPython](https://rustpython.github.io/)

用 Rust 编写的 Python-3 (CPython >= 3.10.0)解释器<g-emoji class="g-emoji" alias="snake" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f40d.png">🐍</g-emoji> <g-emoji class="g-emoji" alias="scream" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f631.png">😱</g-emoji> <g-emoji class="g-emoji" alias="metal" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f918.png">🤘</g-emoji>。

[![Build Status](img/747eb44e55b1716c93a11f5d38f2e2bf.png)](https://github.com/RustPython/RustPython/actions?query=workflow%3ACI)[![codecov](img/81859871a32d1ad9ecda85b3758b7a35.png)](https://codecov.io/gh/RustPython/RustPython)[![License: MIT](img/db85796de36d003cae919db6f133f455.png)](https://opensource.org/licenses/MIT)[![Contributors](img/49ddbf7155e1e24ffa4003a9bcee5b59.png)](https://github.com/RustPython/RustPython/graphs/contributors)[![Gitter](img/fb44ff26ce5c18f8411b7d6a21e85534.png)](https://gitter.im/rustpython/Lobby)[![docs.rs](img/9b5f2a7754a5efec6015abebb7a664c7.png)](https://docs.rs/rustpython/)[![Crates.io](img/a9ec98c5b5bbda8a7e2c5e9f14dce20a.png)](https://crates.io/crates/rustpython)[![dependency status](img/977bff7d76ac0d2a1370d9702eb4e386.png)](https://deps.rs/crate/rustpython/0.1.1)[![WAPM package](img/98a9d2a1b878f648874692d9733d78c9.png)](https://wapm.io/package/rustpython)[![Open in Gitpod](img/58a32bfb425ab6a6ebac7b82f9d69b23.png)](https://gitpod.io#https://github.com/RustPython/RustPython)

## 使用

#### 查看我们在 WebAssembly 上运行的[在线演示](https://rustpython.github.io/demo/)。

RustPython 需要 Rust 最新的稳定版本(例如 2020 年 5 月 24 日的 1.43.0)来检查 Rust 版本:`rustc --version`如果你想更新`rustup update stable`。

要在本地构建 RustPython，请执行以下操作:

```
$ git clone https://github.com/RustPython/RustPython
$ cd RustPython
  # --release is needed (at least on windows) to prevent stack overflow
$ cargo run --release demo.py
Hello, RustPython 
```

或者使用交互式 shell:

```
$ cargo run --release
Welcome to rustpython
>>>>> 2+2
4 
```

注意:对于 windows 用户，请将`RUSTPYTHONPATH`环境变量设置为项目目录中的`Lib`路径。(例如当 RustPython 目录为`C:\RustPython`时，设置`RUSTPYTHONPATH`为`C:\RustPython\Lib`)

您还可以安装并运行 RustPython，包括:

```
$ cargo install --git https://github.com/RustPython/RustPython
$ rustpython
Welcome to the magnificent Rust Python interpreter
>>>>> 
```

…

</article>

[View on GitHub](https://github.com/RustPython/RustPython)

### 你还看到了哪些潜在的生锈用例？