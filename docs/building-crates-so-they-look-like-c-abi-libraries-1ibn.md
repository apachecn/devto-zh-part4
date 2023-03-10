# 建造板条箱，使它们看起来像 C(ABI)图书馆

> 原文：<https://dev.to/luzero/building-crates-so-they-look-like-c-abi-libraries-1ibn>

> 我在 [rustlab 2019](https://rustlab.it) 上展示了 [cargo-c](https://github.com/lu-zero/cargo-c) ，这里是[这个](https://github.com/lu-zero/rustlab-it-2019)更长的后续。

## 混合铁锈和 C

**rust** 的最大卖点之一是与 C-ABI 的高度**互操作性**，此外还有**的安全性**、**的速度**及其令人惊叹的社区。

当您有了**经过良好优化的**手工制作的 asm 内核，并且想按原样使用时，这就非常方便了:

*   它们很小，有一个清晰的界面，通常对它们自己读/写的内容有严格的界限。
*   你基本上可以重写它们，因为它们使用一些[内联汇编](https://doc.rust-lang.org/unstable-book/language-features/asm.html)来获得可疑的收益。
*   cc-rs 和 T2 nasm-RS 都使得构建和链接的过程相对容易。

此外，如果您计划在一个外语项目中集成一些 rust 组件，将 cargo 产生的`staticlib`链接到您的主项目中是非常简单的。

如果你有一个纯 rust 的机箱，你想把它像一个普通的 C(共享/动态)库一样输出到世界上，那就太血腥了。

## 表现良好的 C-API 库结构

通常，当您想在自己的项目中使用 C-library 时，您应该期望它提供以下功能:

*   头文件，告诉编译器它应该期待哪些符号
*   静态库
*   动态图书馆
*   一个 pkg-config 文件给你关于**的指导，在那里**找到头文件，以及**你需要传递什么**给链接器来正确地链接库，不管是静态的还是动态的

### 头文件

在 C 中，你通常在一个单独的文件中保存一个函数原型和类型定义的列表，然后把它嵌入到你的源文件中，让编译器知道会发生什么。

因为你依靠一个非常简单的[预处理器](https://en.wikipedia.org/wiki/C_preprocessor)来完成，你必须小心添加**守卫**，这样文件就不会被包含超过一次，为了避免冲突，你把它安装在你的包含目录的子目录中。

因为标题的位置可能不是默认搜索路径的一部分，所以通常将该信息存储在 **pkg-config** 中。

### 静态库

静态库在概念(和执行)上非常简单:

*   它们是目标代码文件的归档文件。
*   链接器简单地读取它们，就像读取刚刚产生的`.o`一样，并将所有内容链接在一起。

然而有一个**陷阱**:

*   在某些平台上，即使你想创建一个完全静态的二进制文件，你也会因为一些原因而动态地链接一些系统库。>最糟糕的攻击者是 **pthread** 库，在某些情况下是编译器内置的(例如 [`libgcc_s`](https://gcc.gnu.org/onlinedocs/gccint/Libgcc.html) )
*   关于它们是什么的信息通常是未知的

**rustc** 用 [`--print native-static-libs`](https://doc.rust-lang.org/nightly/rustc/command-line-arguments.html#a--print-print-compiler-information) 来拯救，它不是集成的最好例子，因为它是在 **stderr** 上产生的字符串，并且它表现为实际构建的副作用，但它仍然是朝着正确方向迈出的良好一步。

pkg-config 是保存信息并让构建系统知道它的事实上的标准方式(我猜你现在看到了一种模式)。

### 动态库

一个[共享](https://en.wikipedia.org/wiki/Library_(computing)#Shared_library)或动态库是一个特制的可执行代码块，当二进制代码被执行时，它被链接到二进制代码。
与静态链接一切相比，优势主要有两个:

*   **节省磁盘空间**:因为没有链接时间修剪，你最终会携带同一个库的多个副本，每个二进制文件都使用它。
*   **更安全、更简单的更新**:如果您需要更新，比如说 openssl，您只需更新一次，而不需要更新您系统中现有的 100 多个用户。

为了获得正确的功能，存在一些固有的复杂性和限制，最有问题的是 **ABI 稳定性**:

*   动态链接器需要找到二进制文件期望的符号，并使它们具有正确的大小
*   如果您更改结构的内存布局或函数名的表示方式，您应该让链接器知道。

通常，这意味着根据您的平台，您在准备库时应该提供一些版本信息。这可以简单到告诉编译时链接器在库中嵌入版本信息(例如 [Mach-O dylib](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/CreatingDynamicLibraries.html#//apple_ref/doc/uid/TP40002073-SW20) 或 [ELF](https://www.ibm.com/developerworks/library/l-shobj/) )，也可以复杂到制作[版本脚本](https://sourceware.org/binutils/docs/ld/VERSION.html#VERSION)。

与制作一个`staticlib` it 相比，有更多的移动部件和特定于平台的知识。

可悲的是，在这种情况下 **rustc** 现在并没有提供任何帮助:即使 C-ABI 是稳定的，并且是一成不变的，但是 **rust** [mangling 策略](https://github.com/rust-lang/rfcs/pull/2603)还没有最终确定，并且它是 ABI 稳定的很大一部分，所以完全支持动态库的工作还没有完成。

大多数平台中的动态库都有一种方法来存储它们所依赖的其他动态库以及要寻找的路径。当信息不完整，或者您正在以非标准路径存储库时， **pkg-config** 再次伸出援手，帮助您存储信息。

### 包装配置

只要您的构建系统支持它，并且您希望使用的库能够恰当地处理它，它就是您的唯一真理。
如果你想保留一个库的多个版本或者你正在做非系统打包(例如:[家酿](https://brew.sh/)或者 [Gentoo 前缀](https://wiki.gentoo.org/wiki/Project:Prefix))，它会简化你的生活。
除了我上面提到的`search path`、`link line`、`dependency`信息外，还存储了库版本和库间兼容关系。
如果你正在发布一个 C-library 并且没有提供一个`.pc`文件，**请**考虑这样做。

## 从一个箱子中产生一个 C 兼容的库

我解释了我们应该生产什么，现在让我们看看我们能在生锈方面做些什么:

*   我们需要导出 C-ABI 兼容的符号，这意味着我们必须:
    *   用`#[repr(C)]`修饰我们想要导出的数据类型
    *   用`#[no_mangle]`修饰这些函数，并在它们前面加上`export "C"`
    *   告诉`rustc`板条箱类型是`staticlib`和`cdylib`
    *   传递`rustc`平台正确的链接行，这样生成的库里面就有正确的信息。> **注意**:在一些平台上，除了版本信息之外，安装路径也必须在库中编码。
    *   生成头文件，以便 C 编译器知道它们。
    *   用正确的信息生成一个`pkg-config`文件> **注**:这需要知道整个批次最终将被安装在哪里。

`cargo`根本不支持安装库(因为现在 rust 动态库应该**根本不用**)所以我们只能靠自己了。

对于 [rav1e](https://github.com/xiph/rav1e) 我做了艰苦的方式，然后我想出了一个简单的方法供你使用(我曾经用 [lewton](https://github.com/RustAudio/lewton) 花了大约 1/2 天而不是几个~~周~~个月来做同样的事情)。

### 敬酒不吃吃罚酒

> 正如在 [crav1e](https://github.com/lu-zero/crav1e) 中看到的，你可以探索那里的历史。

这并不是完全困难的方式，因为在 [cargo-c](https://github.com/lu-zero/cargo-c) 之前已经有很好的工具来避免一些耗时的任务: [cbindgen](https://github.com/eqrion/cbindgen/) 。
简而言之，我要做的是:

*   想出一个外部构建系统，因为`cargo`本身不能安装任何东西，也不直接知道安装路径信息。我使用了`Make`,因为它简单且足够广泛，任何更丰富的东西都可能碍事，而且设置起来更费时。
*   弄清楚如何提取在`Cargo.toml`中提供的信息，以便我在`Makefile`级别得到它。我放弃并复制了它，因为解析`toml`或`json`对于原型来说是毫无意义的复杂。
*   写下关于如何构建(和安装)库的特定于平台的逻辑。它最终住进了`build.rs`和`Makefile`。再次感谢[德里克](https://github.com/dwbuiten)照顾**窗口**的具体细节。
*   使用 [cbindgen](https://github.com/eqrion/cbindgen/) 生成 C 头(并在此过程中平滑一些粗糙的边缘)
*   因为我们已经有了一个构建系统，所以为了测试和持续集成的目的，需要添加更多的目标。

如果你不想使用 [cargo-c](https://crates.io/crates/cargo-c) ，我在[独立机箱](https://crates.io/crates/cdylib-link-lines)中剥离了`cdylib` -link line 逻辑，所以你可以在你的`build.rs`中使用它。

### 越简单越好

使用一个`Makefile`和一个带有定制的`build.rs`的单独的板条箱工作得很好，并使只关心用 rust 编写的开发人员完全不受上面介绍的血腥细节和装置的影响。

但是它带来了一些额外的变动:

*   保持 API 同步
*   复制发布工作
*   让用户搞不清楚向哪里报告问题或在哪里找到实际的来源。(用户往往会错过显示在明显位置的信息，如自述文件)

所以为了尽量减少它，我想出了一个`cargo` applet，它提供了两个子命令:

*   为了构建这些库。pc 文件和标题。
*   **c 安装**整个批次，如果已经建造或先建造后安装。

它们是两个子命令，因为**以用户身份构建**，然后**以根用户身份安装**是很常见的。如果你正在使用`rustup`并且 root 没有货物，你可以使用`--destdir`然后使用`sudo install`或者手工制作你的本地包，如果你的发行版提供了这样做的方法。

我提到的所有困难都发生在幕后，除了当前实现中的错误，你应该完全忘记细节。

### 使用 cargo-c

> 如在[里看到的莱顿](https://github.com/RustAudio/lewton)和[rave 1e](https://github.com/xiph/rav1e)。

*   [用你想要公开的 C-API 创建](https://github.com/RustAudio/lewton/pull/50/commits/557cb4ce35beedf6d6bfaa481f29936094a71669) a `capi.rs`，当你构建一个普通的 rust 库的时候用`#[cfg(cargo_c)]`隐藏它。
*   [确保](https://github.com/RustAudio/lewton/pull/50/commits/e7ea8fff6423213d1892e86d51c0c499d8904dc1)你有一个`lib`目标，如果你正在使用一个工作空间，第一个成员是你想要导出的板条箱，这意味着你可能需要[在列表](https://github.com/xiph/rav1e/pull/1381/commits/7d558125f42f4b503bcdcda5a82765da76a227e0#diff-80398c5faae3c069e4e6aa2ed11b28c0R94)的开头添加一个`"."`成员。
*   记得在[中添加](https://github.com/RustAudio/lewton/pull/51/files) a `cbindgen.toml`，并至少填充 include guard，可能你想设置语言为`C`(默认为`C++`)
*   一旦你对结果满意，更新你的文档告诉用户安装`cargo-c`和做`cargo cinstall --prefix=/usr --destdir=/tmp/some-place`或者类似的事情。

## 即将到来

cargo-c 是一个年轻的项目，即使它能满足我的需求，也远远没有完成。

帮助改进它是[受欢迎的](https://github.com/lu-zero/cargo-c/issues)，有许多粗糙的边缘和错误要发现和挤压。

## 感谢

感谢 [est31](https://github.com/est31/) 和 [sdroege](https://github.com/sdroege/) 对 **#rust-av** 的深度审查和 [kodabb](https://github.com/kodabb/) 的最后一分钟剪辑。