# 如何在 Ubuntu 上安装 Python 3.8？

> 原文：<https://dev.to/mortoray/how-to-install-python-3-8-on-ubuntu-1bp4>

我无法在 Ubuntu 18.04 上运行 Python 3.8。我已经安装了主程序，但是缺少 pip。

我做到了:

```
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.8 
```

Enter fullscreen mode Exit fullscreen mode

然后作为测试，我也做了:

```
sudo apt install python3.8-distutils

python3.8 -m pip install --upgrade pip setuptools wheel 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我尝试设置一个虚拟 env，它会失败:

```
$ python3.8 -m venv env
Error: Command '['/src/mdl/env/bin/python3.8', '-Im', 'ensurepip', '--upgrade', '--default-pip']' returned non-zero exit status 1. 
```

Enter fullscreen mode Exit fullscreen mode

模块`ensurepip`不存在，也无法安装。

```
$ python3.8 -m ensurepip
/usr/bin/python3.8: No module named ensurepip 
```

Enter fullscreen mode Exit fullscreen mode

我如何安装 Python 3.8，以及一个单独的 pip 和包目录？

* * *

我在下面做了进一步，添加了`-venv`和`-dev`来编译更多的包。

```
sudo apt install python3.8-venv python3.8-dev 
```

Enter fullscreen mode Exit fullscreen mode

起初这似乎不起作用，但不幸的是，这是一个特定包的 3.8 兼容性问题。我想要 3.8 的原因是为了注解支持，所以这很不幸。[他们表示](https://github.com/python/mypy/issues/7001#event-2415987083)一个新的版本将会修复它。