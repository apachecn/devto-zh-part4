# 更好的 Web 应用程序日志记录

> 原文：<https://dev.to/jculverhouse/better-logging-for-the-web-application-2emb>

<figure>[![A journal ledger of accounts... like logging but only uses numbers](img/1a2dbdc7a6c8402a89a55654eaa63c6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SvqAPdc---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/writing-retro-number-old-pattern-line-613428-pxhere.com_.jpg%3Fssl%3D1) 

<figcaption>参见…结构化测井…</figcaption>

</figure>

当我最后一次[离开我的示例 web 应用程序](https://rust.graystorm.com/2019/07/15/its-not-a-web-application-without-a-database/)时，我正在对终端进行简单的登录，这在很长时间内都没有用。我希望[结构化日志](https://stackify.com/what-is-structured-logging-and-why-developers-need-it/)，我希望应用程序和`Rocket`写入(单独)文件。因此，让我们将板条箱`log`和`simple_log`换成`slog`和`sloggers`，以便为我的 web 应用程序获得更好的日志记录。

## 应用日志记录

Slog 的“野心是成为 Rust 的日志库”,这看起来很有适应性。有几个“助手”箱可以记录到终端或系统日志，以 json 等方式记录。但是，`slog`是复杂的！这似乎是`sloggers`出现的主要原因。`Sloggers`带给你`slog`最有用的功能，无需复杂的设置。它碰巧包含了一个通过内嵌 TOML 字符串进行配置的例子。我已经从一个 TOML 文件中获得了我的`CONFIG`全局拉取，所以我将它添加到`conf/development.toml` :

```
<conf/development.toml>

...
[logconfig]
type = "file"
format = "compact"
source_location = "module_and_line"
timezone = "local"
level = "debug"
path = "log/error.log"
rotate_size = 1073741824
rotate_keep = 2

[webservice]
weblog_path = "log/access.log"
... 
```

另外，我添加了`logging.rs`来设置全局`LOGGING`实例，这样每个人都可以登录；就像每个人都可以从全局拉`CONFIG`实例:

```
<logging.rs>

use crate::settings::CONFIG;
use crate::sloggers::{Build, Config};
use once_cell::sync::Lazy;

#[derive(Debug)]
pub struct Logging {
    pub logger: slog::Logger,
}

pub static LOGGING: Lazy<Logging> = Lazy::new(|| {
    let logconfig = &CONFIG.logconfig;
    let builder = logconfig.try_to_builder().unwrap();
    let logger = builder.build().unwrap();

    Logging { logger: logger }
}); 
```

`Sloggers`真的很容易用来建立一个`slog`实例。在这里，我简单地从我的全局`CONFIG`中提取 logconfig，构建日志记录器，并将其存储在我的新 [OnceCell](https://crates.io/crates/once_cell) Lazy `LOGGING`全局中。

## 然后，Web 访问日志记录

[![A spider web, wet from rain...<br>
](img/b8454d89d662aace49084dd44860652a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---PDArqeR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/nature-dew-wing-morning-fauna-material-792967-pxhere.com_.jpg%3Fssl%3D1)

此时，应用程序日志记录将转到我的新的`log/error.log`文件，但是`Rocket`日志记录仍然会出现在屏幕上。这实际上很好——正如我提到的，我想让他们无论如何都要分开文件。所以现在有了一些`slogging`的经验，建立第二档，专门针对`Rocket`就很简单了。我确实需要添加另一个板条箱，这一次是[rocket _ slog](https://crates.io/crates/rocket-slog)——这样`Rocket`的内部人员就可以抓住我制作的`slog`实例。下面是新的`start_webservice`功能:

```
<src/lib.src>

...
pub fn start_webservice() {
    let logger = &LOGGING.logger;
    let weblog_path = &CONFIG.webservice.weblog_path;
    let bind_address = &CONFIG.webservice.bind_address;
    let bind_port = &CONFIG.webservice.bind_port;

    // start rocket webservice
    let version = include_str!("version.txt");
    let mut builder = FileLoggerBuilder::new(weblog_path);

    builder.level(Severity::Debug);

    let weblogger = builder.build().unwrap();
    let fairing = SlogFairing::new(weblogger);
    warn!(logger, "Waiting for connections...";
        "address" => bind_address,
        "port" => bind_port,
        "version" => version);

    rocket::ignite()
        .attach(fairing)
        .attach(Template::fairing())
        .attach(SpaceHelmet::default())
        .mount("/", routes![routes::index, routes::favicon])
        .mount("/img", StaticFiles::from("src/view/static/img"))
        .mount("/css", StaticFiles::from("src/view/static/css"))
        .mount("/js", StaticFiles::from("src/view/static/js"))
        .launch();
} 
```

现在我有了用于应用程序日志记录的`log/error.log`(稍后我会将其重命名为`log/app.log`)和用于`Rocket`日志记录的`log/access.log`。但是我还是没有结构化日志！

## 最后，结构化日志

我尝试了几种方法(用我有限的 Rust 知识和经验),但是看起来很容易使用，如果你想变得有趣，你可以放弃选项。至少，**我**想不出怎么让`slog_json`或者叫`slog_bunyan`的东西和`sloggers`一起工作。看来我不得不直接和`slog`打交道了。

后来，在另一个晚上，我处理这些变化。我转储了`sloggers`，尽管四处搜索，我最终还是合并了我找到的两个例子(分别来自`slog`和`slog`-班扬)。谢天谢地，我在理解贴在板条箱上的 API 文档方面有了一点进步！无论如何，这是现在应用程序日志设置的样子:

```
<src/logging.rs>

...
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

注意，我仍然从`CONFIG`中取出`applog_path`，然后创建文件句柄。接下来，对`slog`的一个简单但复杂的调用返回一个`Logger`实例，我将它存储到我的全局。由`slog` `crate`提供的`o!`宏允许我向写入的每个日志记录添加一个 JSON 字段。我复制了一个示例，并在生成日志的地方添加了文件、行号和模块名。稍后我可能会回到这个话题，添加更多的数据。另外，注意我使用 [slog_bunyan](https://crates.io/crates/slog-bunyan) 进行 JSON 格式化。`slog-json`实际上推荐使用`slog_bunyan`来获得“更完整的输出格式”。[班扬日志](https://nodejs.org/es/blog/module/service-logging-in-json-with-bunyan/)似乎起源于 node.js 模块。最后，我还以同样的方式更改了 weblogger 现在两者都是 JSON 日志，并且仍然记录到单独的文件中。

这实际上没有我担心的那么复杂。我已经失去了一些容易配置的`sloggers`功能，如自动文件旋转，但我也会解决这个问题——这可能实际上只是我需要启用的一个`slog`功能。

## 结果，JSON 样式

这是两者现在的样子——我想下一篇文章我会使用 JSON 日志查看器，这样随着我们的发展，我们可以更容易地查看它们。

```
<log/app.log>

{"msg":"Service starting","v":0,"name":"slog-rs","level":40,"time":"2019-07-20T03:40:13.781382592+00:00","hostname":"ip-169-254-169-254","pid":6368,"location":"src/lib.rs:48 ppslib","run_level":"development"}
{"msg":"Waiting for connections...","v":0,"name":"slog-rs","level":40,"time":"2019-07-20T03:40:13.784899718+00:00","hostname":"ip-169-254-169-254","pid":6368,"location":"src/lib.rs:76 ppslib","version":"0.1.0","port":3000,"address":"0.0.0.0"}

<log/access.log>

{"msg":"listening","v":0,"name":"slog-rs","level":30,"time":"2019-07-20T05:31:21.068959173+00:00","hostname":"ip-169-254-169-254","pid":7700,"address":"http://0.0.0.0:3000"}
{"msg":"request","v":0,"name":"slog-rs","level":30,"time":"2019-07-20T05:31:22.402815352+00:00","hostname":"ip-169-254-169-254","pid":7700,"uri":"\"/\"","method":"Get"}
{"msg":"response","v":0,"name":"slog-rs","level":30,"time":"2019-07-20T05:31:22.404425952+00:00","hostname":"ip-169-254-169-254","pid":7700,"status":"200 OK","route":"\u001b[32mGET\u001b[0m \u001b[34m/\u001b[0m \u001b[36m(\u001b[0m\u001b[35mindex\u001b[0m\u001b[36m)\u001b[0m"} 
```

同样，如果所有这些都很难理解，并且您更喜欢在我经历这一考验时查看 git 提交，请查看[库](https://github.com/jculverhouse/pinpoint_shooting)。

为网络应用更好地记录日志的帖子[首先出现在](https://rust.graystorm.com/2019/07/20/better-logging-for-the-web-application/) [Learning Rust](https://rust.graystorm.com) 上。