# 铁锈存在型

> 原文：<https://dev.to/x1957/rust-existential-type-24j>

Recently, I encapsulated a little lib. I originally wanted to define async fn in trait, but at present it is not allowed. [Async methods I: generic associated types](https://boats.gitlab.io/blog/post/async-methods-i/) , so we can only think of other ways. Then let's call async fn again to return a future, which is the same.

Then the problem came again. I thought it was return an impl Future, but. . . It's not allowed in trait, sad!

[异步方法 I:通用关联类型](https://boats.gitlab.io/blog/post/async-methods-i/)这里也解释了为什么直接关联类型不行。

The reason is that the Future returned by async is a closure, which throws in the lifetime of everything. If we want to write associated types, it needs this:

```
trait Foo {
    type _Future<'a>: Future<Output = i32> + 'a;
    fn foo_method<'a>(&'a self) -> Self::_Future<'a>;
} 
```

But ... . . The problem is coming again. At present, the associated types do not support the lifetime parameter. This thing is called [T0】 generic associated types 【T1]

Fortunately, we still have the existential type, refer to [RFC](https://github.com/rust-lang/rfcs/blob/master/text/2071-impl-trait-existential-types.md)

```
#![feature(async_await, existential_type)]

use futures::compat::Future01CompatExt;
use futures::compat::Stream01CompatExt;
use futures::future::{FutureExt, TryFutureExt};
use futures::stream::{StreamExt, TryStreamExt};
use futures::Future;
use reqwest::r#async::{Client, ClientBuilder, Decoder};
use std::io::{self, Write};

trait Fuck {
    type AsyncResult: Future<Output=()>;
    fn download(&self) -> Self::AsyncResult;
}

struct Down;

impl Down {
    pub fn new() -> Self{
        Down{}
    }
}

impl Fuck for Down {
    existential type AsyncResult: Future<Output=()>;
    fn download(&self) -> Self::AsyncResult {
        download()
    }
}

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

fn call<T: Fuck>(x: T)->T::AsyncResult {
    x.download()
}

fn main() {
    // let fut = download().unit_error().boxed().compat();
    let fut = call(Down::new()).unit_error().boxed().compat();
    tokio::run(fut);
} 
```

We have defined the Fuck, the trait, and there is an associated type AsyncResult. In our impl, the trait can be used to point out that AsyncResult is impl Future by existential type.

You need to turn on featureexisting _ type when using it now.