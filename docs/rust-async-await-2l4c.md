# Rust 异步等待(1)

> 原文：<https://dev.to/x1957/rust-async-await-2l4c>

铁锈版本:

```
rustc 1.38.0-nightly (69656fa4c 2019-07-13) 
```

打开异步/等待的未来:

```
#![feature(async_await)] 
```

```
#![feature(async_await)]

async fn get()->i32 {
    println!("start");
    std::thread::sleep(std::time::Duration::from_millis(1000));
    1
}

async fn p() {
    let n = get().await;
    println!("n = {}", n);
}

fn main() {
    let fut = p();
    println!("called p");
    futures::executor::block_on(fut);
} 
```

A simple get function is defined and returns 1.

P calls get and prints 1.

Async is similar to JavaScript, and it returns future(js's is promise), so we also need an executor to Run this future in Rust. Here, the version we use for future is:

```
futures-preview = { version = "=0.3.0-alpha.17", features = ["compat"] } 
```

The main reason is that async returns std::future::Future, while most of our third-party libraries still use the Future library, such as tokio. If we try with tokio::run, we will get an error

```
 --> src/main.rs:21:5
   |
21 |     tokio::run(fut);
   |     ^^^^^^^^^^ the trait `futures::future::Future` is not implemented for `impl std::future::Future`
   |
   = note: required by `tokio::runtime::threadpool::run`

error: aborting due to previous error

For more information about this error, try `rustc --explain E0277`.
error: Could not compile `async-await`.

To learn more, run the command again with --verbose. 
```

东京::跑是给期货::未来::未来，但是我们异步ˌ非同步(asynchronous)的是标准::未来::未来

Now, the biggest problem is that the third-party library doesn't support std future yet, so if you want to use it, you have to compat it

```
#![feature(async_await)]

use futures::compat::Future01CompatExt;
use futures::future::{FutureExt, TryFutureExt};

async fn get()->i32 {
    println!("start");
    std::thread::sleep(std::time::Duration::from_millis(1000));
    1
}

async fn p() {
    let n = get().await;
    println!("n = {}", n);
}

fn main() {
    let fut = p().unit_error().boxed().compat();
    println!("called p");
    tokio::run(fut);
} 
```