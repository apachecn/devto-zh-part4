# 尝试除了别的尝试..除...之外..其他..python 中的示例

> 原文：<https://dev.to/anuragrana/try-except-else-in-python-with-example-ih0>

本帖最初发表于

 **在使用 python 编码时，您将面临至少两种类型的错误。语法错误和异常。

语法错误也称为解析错误。编译器用箭头通知我们解析错误。

```
rana@brahma:~$ python3
Python 3.5.2 (default, Nov 12 2018, 13:43:14) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> while True print("Hello")
  File "<stdin>", line 1
    while True print("Hello")
                   ^
SyntaxError: invalid syntax
>>> 
```

Enter fullscreen mode Exit fullscreen mode

即使您的代码在语法上是完美的，当您尝试执行它们时，一些行可能会报告错误。运行时报告的错误称为异常。

为了实现不间断的执行，我们必须正确处理异常。我们可以通过使用`try except`块来实现。

在下面的例子中，我们试图添加一个字符串和一个整数。当执行该语句时，它会抛出一个异常。

```
>>> a = 'rana' + 10
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: Can't convert 'int' object to str implicitly
>>> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不处理这个异常，程序将会突然退出。使用`try except`语句处理异常。

```
>>> 
>>> try:
...     a = 'rana' + 10
... except Exception as e:
...     print('Some useful message to debug the code')
... 
Some useful message to debug the code
>>> 
```

Enter fullscreen mode Exit fullscreen mode

当执行上面这段代码时，会抛出一个异常，但随后被`except`块捕获，然后打印一条有用的消息，帮助调试代码。

尝试...except 语句有一个可选的`else`子句。当`try`子句中没有抛出异常时，执行 Else 部分。Else 部分在`finally`子句之前执行。

假设你试图在 try 块中打开一个文件(这只是一个例子),在打开文件时可能会出现一些错误，我们将在 except 块中处理异常。如果没有抛出异常，文件打开成功，我们必须关闭文件描述符。

```
>>> try:
...     f = open('filename.txt','r')
... except Exception as e:
...     print('Some exception in opening the file')
... else:
...     f.close()
... 
```

Enter fullscreen mode Exit fullscreen mode

最好在`else`子句中添加代码，而不是在`try`子句中添加代码。这有助于避免捕获不是由在`try` caluse 中受保护的代码引发的异常。例如，除了在`try`子句中打开文件，我们还试图将变量转换成整数。图像文件打开正常，但转换为 int 抛出了一个异常，该异常将由 except 块报告为打开文件时的异常，这将产生误导。请参见下面的示例代码。

```
>>> try:
...     f = open('filename.txt','r')
...     a = 'five'
...     b = int(a)
... except Exception as e:
...     print('can not open file')
... else:
...     f.close()
... 
can not open file
>>> 
```

Enter fullscreen mode Exit fullscreen mode

上面的内容可以改写如下。

```
try:
    f = open('filename.txt','r')
    print('file was opened')
except Exception as e:
    print('can not open file')
else:
    print('going to close file')
    f.close()
try:
    a = 'five'
    b = int(a)
except Exception as e:
    print('exception in number conversion') 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
file was opened
going to close file
exception in number conversion 
```

Enter fullscreen mode Exit fullscreen mode

这确保 except 子句报告实际的异常。

现在问题来了，我们应该使用`except`还是`else`块来控制执行流程？

在 python 中使用异常来控制执行流程是正常的。例如，迭代器使用`stopIteration`异常来表示项目结束。

您可以通过访问下面参考资料部分的链接来了解更多关于异常的信息。

**参考文献:**

1.  [https://stack overflow . com/questions/16138232/is-it-a-good-practice-to-use-try-except-else-in-python](https://stackoverflow.com/questions/16138232/is-it-a-good-practice-to-use-try-except-else-in-python)
2.  [https://docs.python.org/3/tutorial/errors.html](https://docs.python.org/3/tutorial/errors.html)

图片来源:quotemaster.org

本帖最初发表于

 ****更多来自作者:**

*   [防止 Django 网站遭受跨站点脚本攻击](https://www.pythoncircle.com/post/688/preventing-cross-site-scripting-attack-on-your-django-website/)
*   [如何为 Django 网站生成 Atom/Rss 提要](https://www.pythoncircle.com/post/687/how-to-generate-atomrss-feed-for-django-website/)****