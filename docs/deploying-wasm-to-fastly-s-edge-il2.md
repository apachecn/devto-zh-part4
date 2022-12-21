# 将 WASM 部署到法斯特丽的边缘

> 原文：<https://dev.to/bryanburgers/deploying-wasm-to-fastly-s-edge-il2>

不久前， [Fastly 推出了一个新的边缘编程平台](https://www.fastly.com/blog/edge-programming-rust-web-assembly)。Fastly 的愿景是使用 wasm 来沙盒和运行应用程序，这意味着任何可以编译到 wasm 的语言都可以在他们的边缘服务器上运行。

边缘函数的想法绝不是新的。亚马逊用 Lambda 做，CloudFlare 有 Workers，Google Cloud 和 Azure 都调用他们的函数。

据我所知，Fastly 的产品在使用 wasm 方面是独一无二的，这使他们能够拥有真正快速的启动和响应时间。

算我有兴趣。尽管 Fastly 现在只有有限的预览版(称为 Terrarium)，但我想开始挖掘并开始玩。

## 入门

开始玩玻璃容器的一个很好的地方是他们的[网络编辑器](https://www.fastlylabs.com/#Terrarium)。

web 编辑器有大量有用的例子，是试验一些代码并在 Terrarium 中观察它运行的好地方。查看所有的示例项目，它们非常酷。

## 成长出网络编辑器

最终，您可能会发现 web 编辑器不够强大。对我来说，是当我想引入一个图像处理库的时候。网页编辑器不让你定义自己的依赖关系，所以我被卡住了。

我曾想过将所有来自 [image-rs](https://crates.io/crates/image) 的源代码复制到 web 编辑器中，但是包含所有这些源代码，以及所有可传递依赖项的源代码，会是一个很大的负担。

[lucet](https://github.com/fastly/lucet)(Terrarium 背后的引擎)的说法是，它取 wasm 文件并运行。嗯，我们可以在本地编译 wasm，所以我们应该能够在本地构建项目并运行它，对吗？

我们可以。

## 创建项目

所以让我们开始吧。

```
cargo new --lib terrarium-test 
```

我们遇到的第一个问题是，Terrarium 需要知道要执行什么功能。而且它有一定的生命周期预期。所以我们需要能够进入它的生命周期。

方法是通过他们的 [rust-guest](https://github.com/fastly/terrarium-rust-guest) 库。我还没有在 crates.io 上看到它，但他们已经慷慨地在 GitHub 上提供了它。

所以现在我们可以用构建模块所需的所有东西来建立我们的`Cargo.toml`文件。

```
[lib]
crate-type = ["cdylib"]

[dependencies]
http_guest = { git = "https://github.com/fastly/terrarium-rust-guest" }
image = "^0.22.1" 
```

## 编写代码

这很好，但是我们需要一些实际的代码。这里有一点可以接受任何请求(忽略关于请求的所有内容，包括 URL)，生成一个小图像并用它来响应。

```
use http_guest::{guest_app, Request, Response};
use image::DynamicImage;

/// The entrypoint on Terrarium: called for every HTTP request, and
/// expected to return an HTTP response.
pub fn user_entrypoint(_req: &Request<Vec<u8>>) -> Response<Vec<u8>> {
    let image = generate_image();

    // Turn the image into bytes
    let mut vec: Vec<u8> = Vec::new();
    image
        .write_to(&mut vec, image::ImageOutputFormat::PNG)
        .unwrap();

    // Return the response
    Response::builder()
        .status(200)
        .header("content-type", "image/png")
        .body(vec)
        .unwrap()
}

/// Create a new image to serve.
fn generate_image() -> DynamicImage {
    let mut img = DynamicImage::new_rgb8(256, 256);
    img.as_mut_rgb8()
        .unwrap()
        .enumerate_pixels_mut()
        .for_each(|(x, y, px)| {
            px[0] = x as u8;
            px[1] = y as u8;
            px[2] = (256 - (x as i32 + y as i32)).abs() as u8;
        });
    img
}

// Tell Terrarium about the entrypoint.
guest_app!(user_entrypoint); 
```

好吧，好吧，放我一马。这可能不是最令人信服的例子，但它足够琐碎，适合写在博客上，但又足够复杂，无法在 web 界面上完成。

## 编译代码

我们的代码都准备好了，让我们得到一个 wasm 文件。如果您以前从未编译过 wasm(我没有)，您需要使用`rustup`来下载 wasm 目标。

```
rustup target install wasm32-unknown-unknown 
```

一旦我们有了这些，我们就可以建立这个项目了！出乎意料的简单。

```
cargo build --release --target wasm32-unknown-unknown 
```

如果一切顺利，我们应该会在`target/wasm32-unknown-unknown`中得到一个. wasm 文件。

## 🚀发射它！

如果您在 build.ts 文件的 Terrarium web 编辑器中四处查看，看起来 Terrarium 总是期望模块在`module.wasm`出现。因此，我们可以将我们的模块上传到那个位置，手动禁用`build.ts`，这样我们就不会覆盖它，然后点击“构建&部署”。

但事实证明，有一种更简单的方法，我们不必在 web 编辑器中进行任何手动操作。

Fastly 还提供了一个名为 [terrctl](https://github.com/fastly/terrctl) 的命令行工具，它将为我们从`module.wasm`启动一个应用程序！下载适合您的架构的应用程序，并调用`terrctl`在 Terrarium 上启动。

```
$ cp target/wasm32-unknown-unknown/release/*.wasm module.wasm
$ terrctl module.wasm
[INFO] Preparing upload of directory [module.wasm]
[INFO] Guessed programming language: wasm
[NOTICE] Upload in progress...
[NOTICE] Upload done, compilation in progress...
[INFO] Upload complete, waiting for build...
[INFO] Building...
[INFO] Generating machine code...
[INFO] Codegen complete...
[INFO] Deploy complete: https://involved-mental-write-window.fastly-terrarium.com/
[INFO] Instance is deployed
[NOTICE] Instance is running and reachable over HTTPS
[NOTICE] New instance deployed at [https://involved-mental-write-window.fastly-terrarium.com] 
```

这个命令的输出告诉我们全新的边缘 WASM 模块的 URL！打开 URL，我们应该会看到一个动态生成的图像。呜哇！

## 未来

我真的很喜欢这个。我认为这可能是无服务器功能的巨大进步。我迫不及待地想看到 Fastly 更多的产品化。

现在，有一些限制。您部署的功能只能维持几分钟，这意味着我们目前只能在这里创建短命的玩具。

也没有办法设置秘密。如果你看看他们的 GitHub 例子，他们将 API 令牌硬编码到应用程序中。我可以预见将来他们会允许我们设置环境变量或者使用其他机制来将秘密和配置从代码中分离出来。

但我认为这个产品真的很有前途，所以我认为 Fastly 将很快围绕它建立生态系统。我期待着那一天。边缘的 WASM 前途光明！