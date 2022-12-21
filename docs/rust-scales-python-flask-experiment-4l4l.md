# 锈鳞蟒:烧瓶实验

> 原文：<https://dev.to/tuned/rust-scales-python-flask-experiment-4l4l>

继续这一系列关于如何通过`python-ext-wasmer`将 Wasmer 二进制文件嵌入 Python 模块的简短演示(见前几集 [1](https://dev.to/tuned/rust-scales-python-basic-experiment-49h0) 和 [2](https://dev.to/tuned/rust-scales-python-some-examples-1-2dj5) )。

我想知道利用 Wasm 二进制文件的 Web 应用程序会是什么样子；还是用[烧瓶微框架](https://palletsprojects.com/p/flask/)吧！

我们以通常的方式开始，使用先前编译的 Rust 函数。对于这个实验，我们选择:

*   斐波那契的两个版本，一个作为 Python 函数，一个作为 Wasm 编译的 Rust
*   凸包静态计算的两个版本。

这次我们[将预编译的二进制文件包装到 Flask app](https://github.com/Mec-iS/rust-wasm-python-101/blob/0ad1a42f637d505bac8cf02e594be122900ec2c2/pywasm/examples/flask_basic/__init__.py#L9) 中，并通过两个端点为它们提供服务:

*   [T2`fibo_wasm`](https://github.com/Mec-iS/rust-wasm-python-101/blob/0ad1a42f637d505bac8cf02e594be122900ec2c2/pywasm/examples/flask_basic/app.py#L8)
*   [T2`convexhull_wasm`](https://github.com/Mec-iS/rust-wasm-python-101/blob/0ad1a42f637d505bac8cf02e594be122900ec2c2/pywasm/examples/flask_basic/app.py#L50)

如您所见，就服务性能而言，二进制文件的加载几乎是零成本的，因为它是在应用程序创建时完成的。这允许加载尽可能多的二进制文件，我们需要通过端点来执行处理。对于接受任意参数的 RPC API 来说，这可能是一个好的解决方案。

让我们试着运行它:

【更新】

```
platform linux -- Python 3.6.8, pytest-4.4.1, py-1.8.0, pluggy-0.9.0
rootdir: /home/mec-is/rustcode/wasm-python-101
collected 4 items                                                                                 

pywasm/examples/flask_basic/tests.py 2019-08-18 16:17.29 Time it                        func=fibo_wasm time=0.07568788528442383
.2019-08-18 16:17.35 Time it                        func=fibo_py time=0.06508588790893555
.2019-08-18 16:17.41 Time it                        func=convexhull_wasm time=0.10294783115386963
.2019-08-18 16:17.48 Time it                        func=convexhull_py time=0.4820021390914917 
```

Enter fullscreen mode Exit fullscreen mode

第一次跑还不错！