# 生锈的功能，模块，包装，板条箱，和你

> 原文：<https://dev.to/jculverhouse/rust-functions-modules-packages-crates-and-you-47hl>

<figure>[![Wooden pallets stacked one on top another](img/6aa0e97c021144c7e768967c37048464.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WSuq99eF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/wood-wall-beam-transport-pile-industrial-1358094-pxhere.com_.jpg%3Ffit%3D840%252C560%26ssl%3D1) 

<figcaption>我知道密码就在这里…某个地方。</figcaption>

</figure>

来一探究竟，我是从老文献学铁锈的。我拥有的两本 Rust 印刷书籍都是针对“2018 年版”的，我认为这导致了我对功能、模块、包和板条箱的一些困惑。这本书的一个新版本将在下个月左右推出——我在右边栏有一个亚马逊的链接。如果你一直在阅读[在线文档](https://doc.rust-lang.org/book/)，那就没问题——它更新为“2018 版”。我以前看过 Rust 中的一些[部分，但是我最近发现了另一个新资源，即](https://rust.graystorm.com/2019/07/10/rust-functions-methods-and-traits/)[版本指南](https://doc.rust-lang.org/edition-guide/)，它解决了我的一些问题。这里特别感兴趣的是关于[路径清晰度](https://doc.rust-lang.org/edition-guide/rust-2018/module-system/path-clarity.html)的部分，它受到了改进这部分锈蚀的 [RFC 2126](https://github.com/rust-lang/rfcs/blob/master/text/2126-path-clarity.md) 的严重影响。

我在听第 10 集的[请求解释播客时，了解了 RFC 2126 的一些历史(和兴奋)。无论如何，让我们回到基础，看看 Rust 函数、模块、包和板条箱，因为该语言将于 2019 年年中问世。我将从我的 web 应用程序中展示一些例子。为了简化，我将删除一些不必要的部分，所以“…”意味着为了编译，还有更多的内容。你总能看到它碰巧处于什么状态，这里是](https://podcast.app/two-paths-diverged-in-a-yellow-wood-e22953523/)。

## 板条箱和包裹

铁锈**箱**(像火箭或柴油)是一个二进制或编译代码库。`binary crate`是可运行的，而`library crate`通过与另一个二进制文件链接而用于其功能。一个**包**(就像我的网络应用一样)用一个`Cargo.toml`文件将一个或多个**箱**捆绑在一起。toml 文件配置了**包**的依赖关系和一些关于编译源代码的基本信息。`binary crate`将有一个带有`main()`功能的`src/main.rs`来指导二进制文件的运行。A `library crate`将有一个`src/lib.rs`作为库的顶层。这个顶层指导里面的哪些作品可供库的用户使用。

## 防锈功能

**函数**很简单——源代码中的子程序。一个函数以`fn`开始，可能接收一些参数并可能返回一个值。此外，函数的作用域可以是公共的，也可以是私有的。`src/main.rs`中的`main()`函数是一个特殊的函数，当从命令行调用二进制文件时运行。它决定了你程序的开始，你从那里开始控制。您可以创建其他函数，只是避免保留字(或者使用`r#`前缀来表明您指的是您的函数，而不是保留字，例如`r#expect`，如果您想将一个函数命名为“expect”)。与函数非常相似的是方法和特征，我们在之前已经[研究过了。](https://rust.graystorm.com/2019/07/10/rust-functions-methods-and-traits/) 

```
<src/lib.rs>

...
use diesel::prelude::*;
...
pub fn setup_db() -> PgConnection {
    PgConnection::establish(&CONFIG.database_url)
        .expect(&format!("Error connecting to db"))
} 
```

`setup_db()`是一个相当简单的函数——它不接受任何传入参数，并返回一个名为`PgConnection`的数据库连接`struct`。它在`fn`前有`pub`，表示它是一个“公共”函数。否则，我的 web 应用程序`bin/src/pps.rs`就不能调用这个函数——它就不在范围内。如果没有`pub`，`setup_db()`将只能从 `src/lib.rs`内的*调用。由于我将我的应用程序设计为一个`library crate`，我选择将`setup_db()`放在主`src/lib.rs`文件中。我将用来“运行”我的 web 应用程序的二进制文件在`src/bin/pps.rs`中，它包含一个`main()`函数。*

我们来看返回类型，`PgConnection`。这是由数据库 ORM 库定义的`struct`，[柴油](https://diesel.rs/)。我可以编写返回这种特殊类型的`struct`的函数的唯一方法是因为我在顶部有`use diesel::prelude::*;`(它也在 toml 文件中)。柴油图书馆板条箱提供了[前奏](https://docs.diesel.rs/diesel/prelude/)作为一种简单的方式来引入所有柴油必须提供我的包。Diesel 提供了`PgConnection`结构作为`public`(或者说`crate`有什么用)，所以我现在可以在我的代码中使用那个`struct`。这也给了我(方法或特质，你如何分辨？)`establish()`。就像您调用`String::new()`获取一个新字符串一样，我调用`PgConnection::establish()`获取一个新的数据库连接，然后返回它(看，没有尾随；就行了)。

## 锈模块

功能(和其他东西)可以组合成一个**模块**。例如，`setup_logging()`也在`src/lib.rs`中。然而，我可以把它包在一个名为`module`的里面，就像这样:

```
<src/lib.rs>

...
pub mod setting_up {
...
    use logging::LOGGING;
    use settings::CONFIG;
    pub fn setup_logging() {
        let applogger = &LOGGING.logger;
        let run_level = &CONFIG.server.run_level;
        warn!(applogger, "Service starting"; "run_level" => run_level);
     }
} 
```

现在它是我的`setting_up`模块的一部分。这里，模块也需要是`pub`，这样我的应用程序就可以使用它和其中的公共函数。现在模块`setting_up`中的所有枚举、结构和函数都包含在一起了。只要它们是公开的，我仍然可以在我的应用程序中访问它们。

注意，I `use logging::LOGGING;`和`use settings::CONFIG;`引入了这两个`structs`，所以我可以使用应用程序启动时构建的全局静态。我在顶层的`src/lib.rs`中加入了`pub mod logging;`和`pub mod settings;`，所以它们可以在我的应用程序中更深的任何地方找到。我只需要`use`它们，因为我在这个模块的代码中引用了它们。

[![Splitting firewood with an axe](img/26e3221e286c7531caf5735f657095ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VXh1PHil--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/branch-sharp-wood-warm-trunk-tool-1233390-pxhere.com_.jpg%3Fresize%3D512%252C342%26ssl%3D1)

## 拆分，为清晰起见

另一方面，您可以使用不同的文件来表示一个模块，而不是像上面那样在一个文件中定义一个模块或多个模块。这有助于拆分和分离你的代码，使之更容易一次接受一点。我在这里用`logging.rs` :
做了这个

```
<src/logging.rs>

...
use slog::{FnValue, *};

pub struct Logging {
    pub logger: slog::Logger,
}

pub static LOGGING: Lazy<Logging> = Lazy::new(|| {
    let logconfig = &CONFIG.logconfig;
    let logfile = &logconfig.applog_path;

    let file = OpenOptions::new()
        .create(true)
        .write(true)
        .truncate(true)
        .open(logfile)
        .unwrap();
    let applogger = slog::Logger::root(
        Mutex::new(slog_bunyan::default(file)).fuse(),
        o!("location" => FnValue(move |info| {
            format!("{}:{} {}", info.file(), info.line(), info.module(), )
          })
        ),
    );
    Logging { logger: applogger } }); 
```

我有它的一个`struct`和一个`static`实例，它们都是公共的，在`logging.rs`中定义。`logging.rs`当我指定它时，它会成为我的库箱的一个模块。在`src/lib.rs`的顶部，我有`pub mod logging;`，这表示我的库箱使用那个模块文件`logging.rs` **和**“导出”它从那个模块获得的公共文件(所以我的`bin/src/pps.rs`应用程序可以使用它提供的文件)。

在这种情况下，您还可以看到 I `use slog::{FnValue, *}};`，它类似于`use slog::FnValue;`(我需要它用于`FnValue struct`)和`use slog::*;`，它给了我`fuse struct`和`o!`宏。我能够将这些组合成一个单独的`use`语句，从那个外部`crate`中得到我所需要的。

我参考的旧书让你在你的`Cargo.toml`文件中声明你想在你的应用程序中使用的第三方`crates`(这仍然是必需的)，但是你也必须在`main.rs`或`lib.rs`的顶部带一个`extern crate each_crate;`。谢天谢地，99%的时候不再需要这样了。事实上，我自己也有一个很长的清单——我很惊讶`cargo build`没有提醒我这是不必要的。事实上，我确实有一台`crate`正在使用，它仍然需要这个“2015 版”要求:柴油。显然，它正在做一些花哨的宏观工作和/或还没有升级(还没有？)对于 Rust 的“2018-edition”，所以在`src/lib.rs`的顶部，我有:

```
#[macro_use]
extern crate diesel; 
```

## 几个标准和 TOMLs

锈箱`std`是[标准库](https://doc.rust-lang.org/std/)，自动包含。原始数据类型和宏和关键字的健康列表都包括在内。但是，如果你需要[文件系统工具](https://doc.rust-lang.org/std/fs/index.html):`use std::fs`；如果你需要一个 [HashMap](https://doc.rust-lang.org/std/collections/struct.HashMap.html) 变量，你将需要使用`std::collections::HashMap;`，是的，所有你在你的源中依赖的外部箱子将需要在`Cargo.toml`中列出。这个配置对你有帮助——当次要版本可用时，它会自动更新`crates`,但是如果发布了主要版本，它不会更新。您需要手动完成这项工作，这样您就可以测试一下主版本是否破坏了您代码中所依赖的任何东西。到目前为止，这是我为 web 应用程序编写的一个不断增长的`Cargo.toml`文件:

```
...
[dependencies]
    slog = "2.5.0"
    slog-bunyan = "2.1.0"
    base64 = "0.10.1"
    rand = "0.7.0"
    rand_core = "0.5.0"
    rust-crypto = "0.2.36"
    config = "0.9.3"
    serde = "1.0.94"
    serde_derive = "1.0.94"
    serde_json = "1.0.40"
    once_cell = "0.2.2"
    dotenv = "0.14.1"
    chrono = "0.4.7"
    rocket = "0.4.2"
    rocket-slog = "0.4.0"

[dependencies.diesel]
    version = "1.4.2"
    features = ["postgres","chrono"]

[dependencies.rocket_contrib]
    version = "0.4.2"
    default-features = false
    features = ["serve","handlebars_templates","helmet","json"] 
```

帖子[锈函数、模块、包、板条箱、你](https://rust.graystorm.com/2019/07/23/more-on-rust-functions-modules-packages-and-crates/)最早出现在[学锈](https://rust.graystorm.com)上。