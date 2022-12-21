# listdir vs scandir vs glob——这是唯一一种显而易见的方法！

> 原文：<https://dev.to/prahladyeri/listdir-vs-scandir-vs-glob-the-one-and-preferably-only-one-obvious-way-to-do-it-6hl>

你知道，有时候当我读到那些 python 格言，比如“美丽胜于丑陋”，我想知道制作者是在讽刺还是真实的，我不是在开玩笑！

不仅仅是关于`listdir`和`scandir`，很多事情都是模糊的，你会发现很多不同的方法来做同样的事情，从而导致一个不标准和混乱的工作系统。setuptools 和 distutils 是明显的例子，有如此多的 setuptools 组件仍然在内部使用旧的 distutils 包，这令人惊讶。easy_install 是完成相同打包任务的另一种方式！

有时候，shutil 中的一个功能被破坏了，你需要从旧的 distutils 中导入替代功能，这是非常荒谬的。最近，我偶然发现了这个

```
from distutils.dir_util import copy_tree 
```

Enter fullscreen mode Exit fullscreen mode

shutil.copy_tree 坏了(它不会自动创建不存在的文件夹，还有其他一些不规则的地方)，所以我不得不使用 distutils.dir_util 模块中的 copy_tree。

这是另一个很好的例子。`distutils.core`、`pkg_resources`还有另一个叫做`site`的软件包，它们基本上都在做同一件事。在这种情况下，我不得不编写一个本地版本的`pip --freeze`来检查我的本地 setup.py 并从中生成 requirements.txt，而不是全局安装的模块。正如你所看到的，也许有很多令人困惑的方法来实现同样的事情:

```
#!/usr/bin/env python import os
import distutils.core
import pkg_resources

if __name__ == "__main__":
    setup = distutils.core.run_setup("setup.py")
    ss = ""
    for req in setup.install_requires:
         vv  = pkg_resources.get_distribution(req).version
         ss += "%s==%s\n" % (req, vv)
    ss = ss.strip()
    print(ss)
    open('requirements.txt','w').write(ss)
    print("Written to requirements.txt") 
```

Enter fullscreen mode Exit fullscreen mode

所以，请再告诉我一次，这是真的还是讽刺！

```
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
**There should be one-- and preferably only one --obvious way to do it.**
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those! 
```

Enter fullscreen mode Exit fullscreen mode