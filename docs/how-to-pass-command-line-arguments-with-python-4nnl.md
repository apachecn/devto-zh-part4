# 如何用 Python 传递命令行参数

> 原文：<https://dev.to/tonetheman/how-to-pass-command-line-arguments-with-python-4nnl>

一旦你用 Python 写了几个程序，当你运行程序时(在调用时)，你会想把参数传递给那些程序。

这是 Python 使用 sys 模块完成的。在尝试访问命令行传递的参数之前，您需要将该模块导入到您的程序中。

sys 模块中还有很多其他东西的用处。看这里所有其他的东西:[https://docs.python.org/3/library/sys.html](https://docs.python.org/3/library/sys.html)

要将程序的参数打印出来，需要访问一个名为 sys.argv 的变量，argv 是参数值的缩写。

```
import sys
print(sys.argv) 
```

Enter fullscreen mode Exit fullscreen mode

要注意的主要事情是 sys.argv 的类型是一个字符串列表。sys.argv[0]是 Python 脚本的名称。

如果你像这样运行上面列出的 Python 程序:

```
python myprogram.py 1 a b 
```

Enter fullscreen mode Exit fullscreen mode

那么 sys.argv [0](https://dev.toor%20the%20first%20element%20in%20the%20list) 将是您的 Python 脚本的名称。

```
['myprogram.py', '1', 'a', 'b'] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，sys.argv 是一个字符串列表。如果你的程序传递一个数字(正如我在例子中所做的)，这个数字将作为一个字符串进入你的 Python 程序。