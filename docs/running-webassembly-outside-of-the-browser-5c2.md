# 在浏览器外运行 WebAssembly

> 原文：<https://dev.to/d3spis3d/running-webassembly-outside-of-the-browser-5c2>

这是关于 Rust、WebAssembly 和遗传算法系列文章的第三篇。如果你还没有阅读本系列的第[篇第](https://dev.to/d3spis3d/genetic-algorithm-in-rust-3gg)篇或第[篇第](https://dev.to/d3spis3d/genetic-algorithm-in-the-browser-with-webassembly-1e97)篇文章，那么在继续阅读之前，绝对有必要再看一遍。在本文中，我将讨论 Wasmtime，一个在浏览器之外执行 WebAssembly 的运行时。我将采用本系列第一篇文章中开发的算法，构建一些额外的特性来改进命令行的使用，编译成 WebAssembly 并使用 Wasmtime 从命令行运行 WebAssembly 模块。

这篇文章的完整代码可以在[这里](https://github.com/d3spis3d/wasi-genetic)找到。

[![Abstract image of green and white swirls](img/d663aa1387cbfd8253b82e7961442392.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jGBgmIL5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wdf2e4c43o3wquj2xop9.jpg) 
照片由[达里奥·门德斯](https://unsplash.com/@dariomen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/t/experimental?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我对本系列第一篇文章中 Rust 代码的第一个改进是将算法参数作为命令行参数。为了实现这一点，我添加了`structopt` [机箱](https://crates.io/crates/structopt)，它构建在流行的`clap` [机箱](https://crates.io/crates/clap)之上，并允许使用 struct 定义命令行参数。为了定义 CLI 参数，定义了一个结构，并与宏和来自`structopt`的自定义派生一起使用。

```
#[derive(StructOpt)]
#[structopt()]
struct Opt {
    #[structopt(name = "iterations")]
    iterations: usize,
    #[structopt(name = "pop_size")]
    population_size: usize,
    #[structopt(name = "crossover_rate")]
    crossover_rate: f64,
    #[structopt(name = "mutation_rate")]
    mutation_rate: f64,
    #[structopt(name = "survival_rate")]
    survival_rate: f64,
    #[structopt(name = "csv", parse(from_os_str))]
    csv: PathBuf,
}

fn main() {
    let opts = Opt::from_args();
    ...
} 
```

第二个变化是从 CSV 文件中解析旅行推销员问题的城市列表。CSV 将有两列，第一列是每个城市的 x 坐标，第二列是 y 坐标。CSV 文件的第一行将包含一个带有列名(x 和 y)的标题，这将允许这些行在被读入时通过`csv` [箱](https://crates.io/crates/csv)被描述为**城市**结构。

```
#[derive(Deserialize)]
pub struct City {
    x: f64,
    y: f64,
}

fn main() {
    ...
    let mut reader = Reader::from_path(opts.csv).unwrap();
    let cities: Vec<City> = reader.deserialize()
        .map(|r| {
            let result: City = r.unwrap();
            result
        })
        .collect();

    ...
} 
```

要开始使用 Wasmtime，您需要从源代码编译它。关于设置的全部细节可以在[这里](https://github.com/CraneStation/wasmtime/)找到，但是对于 MacOS 来说这涉及到:

```
brew install cmake llvm

git clone --recurse-submodules https://github.com/CraneStation/wasmtime.git

cd wasmtime

cargo build 
```

Wasmtime 编译完成后，下一步是确保 WebAssembly 所需的编译目标可用。Wasmtime 使用 WebAssembly 系统接口(WASI ),该接口为 WebAssembly 代码提供对操作系统功能(如文件系统)的访问。使用:
添加正确的编译目标并将 Rust 代码编译到 WebAssembly 中

```
rustup target add wasm32-wasi

cargo build --target wasm32-wasi --release 
```

最后用 Wasmtime 在命令行运行 WebAssembly 遗传算法。WebAssembly 的安全模型涉及沙盒，这意味着为了使程序能够从操作系统访问文件，Wasmtime 运行时需要一个允许程序访问的目录列表。在本例中，提供了当前目录`.`,以便 WebAssembly 模块可以访问那里的 cities.csv 文件。

```
../wasmtime/target/release/wasmtime --dir=. target/wasm32-wasi/release/wasi-genetic.wasm 5000 500 0.4 0.001 0.3 cities.csv 
```

感谢阅读这一系列关于 Rust、WebAssembly 和遗传算法的帖子。任何问题、反馈或评论都欢迎在这里或在列出的存储库上提出。这篇文章的完整代码可以在[这里](https://github.com/d3spis3d/wasi-genetic)找到。