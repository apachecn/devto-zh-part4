# 拼凑 Rust Web 应用程序

> 原文：<https://dev.to/jculverhouse/piecing-together-a-rust-web-application-3d30>

<figure>[![](img/6d75580adbfd8c8b6557970e8f1bd1be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NfFgcs8f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/leaf-petal-number-green-color-material-1110050-pxhere.com_.jpg%3Ffit%3D840%252C560%26ssl%3D1) 

<figcaption>Hrm…如何在 [Crates.io](https://crates.io) 上找到合适的棋子？？？</figcaption>

</figure>

十多年来，我一直用 Perl 开发 web 应用程序，最近几年用 Catalyst/Moose/DBIC 和一系列内部抽象。有一堆我期望在任何 web 应用中需要的特性:配置文件(在不同的平台级别)；结构化日志记录；数据库 ORM 模板化；cookie、认证和会话控制；访问机密加密/解密；等等。我花了大部分的时间玩拼凑 Rust web 应用程序，尽管我还有很多事情要做。在为 279 行 Rust 代码奋斗了几个小时后，我认为这是值得的。我会试着探索我的一些问题和我解决的方法。这可能需要一个以上的职位，所以我不把你睡觉。

## 全局(静态)应用配置

这可能不是习惯性的生锈，因为更多的原因而不被接受。我相信随着我的学习，我会适应乡村思维，但现在，我喜欢这样。我喜欢有一个在初始化时设置好的并且不可变的`Config struct`(我敢肯定，Rust 编译器听起来很舒服)。我与`const`和`lazy_static`以及其他许多事情进行了斗争。最终，我选定了作者[似乎无意中写/出版的一个箱子](https://internals.rust-lang.org/t/pre-rfc-lazy-static-move-to-std/7993/38)(除非我遗漏了一些上下文): [OnceCell](https://crates.io/crates/once_cell) 。当我很难让`lazy_static`工作的时候，`once_cell::sync::OnceCell`似乎很快就为我工作了。

除此之外，我喜欢让配置设置由 YAML 或 JSON 或 TOML 文件初始化的概念，并且能够以某种方式被覆盖——通常是环境变量。这条路(以及更早的[帖子](https://rust.graystorm.com/2019/06/28/config-issues/))把我带到了名副其实的[配置](https://crates.io/crates/config)机箱。它正好满足了我从不同地方将设置拖入配置的需要。我最后也添加了 [dotenv](https://crates.io/crates/dotenv) 箱，因为其他东西在一个例子中使用了它。我不确定我会永远保留所有这些选项，但现在还在考虑中。显然，除了 ENV 变量之外，有许多方法可以允许覆盖或者保护磁盘上的设置，也有许多方法可以决定在哪个平台上运行——我很灵活。

编辑:我刚刚意识到把`<Mutex>`放在我的类型上意味着我必须`lock()`它来读取它——这阻止了其他函数(和其他线程)读取它。因为我同意 CONFIG 是不可变的，所以我真的不需要互斥体，所以我放弃了它。

我的 settings.rs 模块暂时是这样的:

```
use config::{Config, Environment, File};
use dotenv::dotenv;
use once_cell::sync::Lazy;
use serde_derive::Deserialize;
use std::env;

#[derive(Debug, Deserialize)]
pub struct Server {
    pub run_level: String,
}

#[derive(Debug, Deserialize)]
pub struct WebService {
    pub bind_address: String,
    pub bind\_port: u16,
}

#[derive(Debug, Deserialize)] 
pub struct Settings {
    pub server: Server,
    pub webservice: WebService,
    pub database_url: String,
}

pub static CONFIG: Lazy<Settings> = Lazy::new(|| {
    dotenv().ok();
    let mut config = Config::default();
    let env = env::var("PPS\_RUN\_MODE")
        .unwrap\_or("development".into());
    config
        .merge(File::with_name("conf/default"))
        .unwrap()
        .merge(File::with_name(&format!("conf/{}", env))
            .required(false))
        .unwrap()
        .merge(File::with_name("conf/local")
            .required(false))
        .unwrap()
        .merge(Environment::with_prefix("PPS"))
        .unwrap();

    match config.try_into() {
        Ok(c) => c,
        Err(e) => 
            panic!("error parsing config files: {}", e),
    }
}); 
```

和配置文件，例如:

conf/default.toml:

```
[server] run_level = "default" 
```

conf/development.toml:

```
[server]
run_level = "development"

[webservice]
bind_address = "0.0.0.0"
bind_port = 3000 
```

conf/staging.toml:

```
[server]
run_level = "staging"

[webservice]
bind_address = "0.0.0.0"
bind_port = 3000 
```

还有，conf/production.toml:

```
[server]
run_level = "production"

[webservice]
bind_address = "0.0.0.0"
bind_port = 80 
```

## 应用日志记录

拼凑一个 Rust web 应用程序包括另一个大问题——日志记录！日志记录很容易成为带宽和存储空间的巨大负担，但是一个日志记录可能解释一个生产事件，并引导您快速修复！结构化日志对于日志平台来说是非常好的，因为当消息是静态的并且附加到日志记录的数据字段填充了可变的间隙时，存储和特别是搜索可以得到极大的改进。

为了让事情进展顺利，我从板条箱[日志](https://crates.io/crates/log)和[简单日志](https://crates.io/crates/simple_logger)开始，但是我可能会转移到[日志](https://crates.io/crates/slog)进行结构化日志记录。我的 main()中的第一行是调用 setup_logging()，因此如果应用程序初始化时出现任何问题，我们都应该得到一个日志。由于 CONFIG 是全局静态的，这个简单的函数现在看起来是这样的，但很快我会在设置中指定日志级别，这样对开发人员来说会很冗长，但对生产来说会更温和:

```
pub fn setup_logging() {
    simple_logger::init_with_level(log::Level::Info)
        .expect("Trouble starting simple_logger");

    let run_level = &CONFIG.server.run_level;
    warn!("Running as run_level {}", run_level);
} 
```

我们仍然有 web 框架、数据库、加密和更多的东西。接下来，更多的单字板条箱:铁、火箭、柴油。我很想听听 Rust 开发人员到目前为止对此的看法——欢迎提出建议。这是 Github 上的[库](https://github.com/jculverhouse/pinpoint_shooting)——你可以跳过来看看我还做了什么。

拼凑一个 Rust Web 应用的帖子最早出现在[学 Rust](https://rust.graystorm.com) 上。