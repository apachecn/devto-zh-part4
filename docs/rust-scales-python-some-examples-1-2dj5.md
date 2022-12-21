# 锈鳞 Python:一些例子(1)

> 原文：<https://dev.to/tuned/rust-scales-python-some-examples-1-2dj5>

在继续这篇文章之前，请阅读这篇文章的第[部分。](https://dev.to/tuned/rust-scales-python-basic-experiment-49h0)

## 简介

在本系列的第一篇文章中，我展示了利用 [python-ext-wasm](https://github.com/wasmerio/python-ext-wasm/tree/master) 所需的基本命令。Wasmer.io 是一个项目，旨在提供一个简化的解决方案，允许通过 WebAssembly 编译和使用“通用二进制文件”； **python-ext-wasm** 是一个库，它提供了从 python 到编译后的二进制文件的绑定，因此将来任何 Python 开发者都可以通过继续编写 Python 代码来利用所有令人难以置信的 WebAssembly 特性。

## 环境和假设

正如上一篇文章所描述的，我们在不影响人体工程学和易用性的前提下，使用了多样化的设置。开始实验的基本组件是 Rust(和`cargo`)和 Python。为了练习工作流，我已经发现了一些将成为我们目标的函数，并提供了一些 Wasmer.io 方法的有用性的基本度量。这些函数是用 Rust 编写的:

1.  一个简单的加法函数:

    ```
    pub extern fn simple_add(a: i32, b: i32) -> i32 { a + b} 
    ```

2.  一个斐波那契数列的实现，这是你在尝试练习你的编码技能时可能用过的众多实现之一([学分在这里](https://github.com/eliovir/rust-examples/blob/master/fibonacci.rs) ):

    ```
    #[no_mangle]
    pub extern fn fibo(n: i32) -> i32 {
    if n < 0 {
        panic!("{} is negative!", n);
    } else if n == 0 {
        panic!("zero is not a right argument to fibonacci()!");
    } else if n == 1 {
        return 1;
    }

    let mut sum = 0;
    let mut last = 0;
    let mut curr = 1;
    for _i in 1..n {
        sum = last + curr;
        last = curr;
        curr = sum;
    }
    sum
    } 
    ```

3.  用于计算一组笛卡尔点上的凸壳的函数，取自 Rust [geo](https://github.com/georust/geo)

    ```
    use geo::{Polygon, LineString};
    use geo::convexhull::ConvexHull;
    ///
    /// Constructive operations: Convex hull in Rust Geo
    ///
    #[no_mangle]
    pub extern fn rust_geo_convex_hull() -> () {
    // An L shape
    let coords = vec![
        (0.0, 0.0),
        (4.0, 0.0),
        (4.0, 1.0),
        (1.0, 1.0),
        (1.0, 4.0),
        (0.0, 4.0),
        (0.0, 0.0)];
    // conversions to geo types are provided from several kinds of coordinate sequences
    let poly = Polygon::new(coords.into(), vec![]);

    // uses the QuickHull algorithm to calculate the polygon's convex hull
    let hull = poly.convex_hull(); 
    ```

我挑选了这些函数，这样就有可能将它们与 Python 中它们自己的等价函数进行比较，你可以[在这里找到它们的编码](https://github.com/Mec-iS/rust-wasm-python-101/blob/master/timing/src_py/__init__.py):

1.  Python 中最基本的加法函数
2.  取自 SO 的 Fibonacci 实现使用了 while 循环(与上面使用 for 循环的 Rust 实现一样)
3.  由最流行的 Python geolibrary 之一的[`shapely`](https://pypi.org/project/Shapely/)(`pip install shapely[vectorized]`)提供的凸包实现。已经传递了与其 Rust 对应物相同的输入。

## 运行测试

如果您已经安装了符合要求的 Python 虚拟环境，并且遵循了前一篇文章中的简单说明，您也许能够[克隆 repo](https://github.com/Mec-iS/rust-wasm-python-101) 并自己尝试:

*   在根目录下，用上面的 Rust 函数编译你的通用二进制文件；
*   在`timing`目录下运行`python run_timing_collection.py`。这是一系列的`timeit`调用，为每个函数的两个实现计时

这是我的机器打印的结果，列出了 Python 函数计时和 Wasm 函数计时，经过了几千次迭代:

```
Modules exported from Rust: 
["loop_str", "rust_geo_convex_hull", "fibo", "simple_add"]
py add 1.2614150420049555
t_wasm add 8.43558448299882
py fibo 44.35585713999899
t_wasm fibo 1.240821371000493
py shapely convex hull 47.863506109999435
t_wasm rust-geo convex hull 2.2532036840057117 
```

Enter fullscreen mode Exit fullscreen mode

你应该也能在`timing/data/timing.csv`中找到一些数据

*免责声明*:我不认为这是一个合适的基准，而是一个练习，所以我不期望结果是准确的，而只是给出一个趋势的一般指示。

## 结论

我将很高兴从不同的设置收到更多的数据。如果你愿意，你可以把你的评论和你的机器打印出来的结果放在一起；只需复制/粘贴您的`timing.csv`中的最后一行来帮助处理一些数据。

感谢所有开源维护者让这个简短的实验成为可能。