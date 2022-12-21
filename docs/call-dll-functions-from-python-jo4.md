# 从 Python 调用 DLL 函数

> 原文：<https://dev.to/petercour/call-dll-functions-from-python-jo4>

你有 C 语言编程的经验吗？

可以用 ctypes 从 [Python](https://pythonprogramminglanguage.com/) 调用 C 函数。ctypes 是什么？

ctypes 是 Python 的外来函数库。它提供了与 C 兼容的数据类型，并允许在 dll 或共享库中调用函数。它可以用来用纯 Python 包装这些库。

在 Python 2.x 中是这样做的(如果你还有的话):

```
#!/usr/bin/python
from ctypes import *

libc = cdll.LoadLibrary("/lib/x86_64-linux-gnu/libc.so.6")
printf = libc.printf
printf("hello world\n") 
```

对于 Python 3.x 程序(是的，区别是一个字符)

```
#!/usr/bin/python3
from ctypes import *

libc = cdll.LoadLibrary("/lib/x86_64-linux-gnu/libc.so.6")
printf = libc.printf
printf(b"hello world\n") 
```

确保共享库(libc.so.6)的路径是正确的。

[![](img/88147d5809fb50fe1754509708debc67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q0YbO89i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Fsocialnewsdaily.com%252Fwp-content%252Fuploads%252F2016%252F07%252Funnbgkdbmsszmazgxkmr.jpg%26f%3D1)

很可能在另一个地方。在 Windows 或 Mac 上有不同的路径和名称。否则就是非常基础的，这应该对任何 C 库都管用。

相关链接:

*   [Ctypes 初学者示例(Python+C)](https://stackoverflow.com/questions/5081875/ctypes-beginner#5082294)
*   [Ctypes 文档](https://docs.python.org/2/library/ctypes.html)
*   [学习 Python 编程](https://pythonprogramminglanguage.com/)