# Rust 异步等待(2)

> 原文：<https://dev.to/x1957/rust-async-await-2-58ha>

[Rust async await (1)](https://dev.to/x1957/rust-async-await-2l4c) In it, we mentioned the current syntax of simple asyncawait and wanted to try it in actual combat.

Let's try it with asynchronous http request, depending on the following:

```
[dependencies]
tokio = "0.1.22"
futures-preview = { version = "=0.3.0-alpha.17", features = ["compat"] }
reqwest = "0.9.18" 
```

```
#![feature(async_await)]

use futures::compat::Future01CompatExt;
use futures::compat::Stream01CompatExt;
use futures::future::{FutureExt, TryFutureExt};
use futures::stream::{StreamExt, TryStreamExt};
use futures::Future;
use reqwest::r#async::{Client, ClientBuilder, Decoder};
use std::io::{self, Write};

async fn download() {
    let client = ClientBuilder::new().build().unwrap();
    let res = client
        .get("https://dev.to/x1957/rust-async-await-2l4c")
        .send()
        .compat()
        .await;
    let mut body = res.unwrap().into_body().compat();
      while let Some(next) = body.next().await {
        let chunk = next.unwrap();
        io::stdout().write_all(&chunk).unwrap();
    }
    println!("\nDone");
}

fn main() {
    let fut = download().unit_error().boxed().compat();
    tokio::run(fut);
} 
```

Note:
1\. *Use reqwest:: r # async:: {client, client builder, decoder}* Because Reqwest has not been upgraded, the previous async is now a keyword, Therefore, you have to use r #
2, the future after. send is futures01 and needs compat
3, and the stream after into_body is stream01 and needs compat.