# 刚刚开始使用 pyxel

> 原文：<https://dev.to/tonetheman/just-getting-started-with-pyxel-1o20>

我从这条推文[https://twitter.com/kitao/status/1152919321032589317](https://twitter.com/kitao/status/1152919321032589317)
中得到写这篇文章的灵感。这条推文很难读，但仍然非常酷。

pyxel 是一种学习/使用 Python 的有趣方式。您可以编写易于交互的程序，并且不需要考虑图形编程。

看这里 github 回购:[https://github.com/kitao/pyxel](https://github.com/kitao/pyxel)

首先进行 pip 安装

```
pip install pyxel 
```

Enter fullscreen mode Exit fullscreen mode

然后输入并运行它

```
import pyxel
from random import random as r
pyxel.init(64,64)

def update():
    if pyxel.btnp(pyxel.KEY_Q):
        pyxel.quit()

def draw():
    pyxel.cls(0)
    pyxel.line(r()*64,r()*64,r()*64,r()*64,7)

pyxel.run(update,draw) 
```

Enter fullscreen mode Exit fullscreen mode

你会得到这样的结果:
[![](img/7e2a5a7e53ba4495e650622e9511fa03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kyHEw7-0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzev521vrpb9ccvi9rsn.gif)