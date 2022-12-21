# Python 中的上下文管理器

> 原文：<https://dev.to/pradhvan/context-managers-in-python-26ng>

# Python 中的上下文管理器

**Python 中的上下文管理器**帮助用户有效地管理程序中的资源，即打开或关闭资源或锁定或解锁资源。当我们必须在事件之前和之后管理资源时，上下文管理器就派上了用场。

每个 python 程序员管理文件最常用的上下文管理器是带有 as 语句的*。*

```
with open('MyContextManager.txt') as f:
    f.write('Using Context Manager is FUN !') 
```

上面代码片段主要有两个优点:

*   有助于自动有效地关闭资源。这可能是一个很小的代码块，所以文件被正确地打开和关闭是显而易见的，但是如果函数的范围增加了呢？这就是为什么上下文管理器真的出现了。
*   使代码可读，复杂的逻辑变得简单。上面的代码也可以写成:

```
file = open('MyContextManager.txt','W')
try:
    file.write('Not using a Context Manager.')
finally:
    file.close() 
```

这里我们用一个 *try-finally* 语句手动管理文件的打开和关闭。

Python 的标准库附带了一个模块， *contextlib* 。这包含了使用上下文管理器和带有语句的*的实用程序。*

#### 编写自己的上下文管理器

那么，为什么有人想要编写自己的上下文管理器呢？

因为，上下文管理器最擅长在事件之前和之后管理资源。因此，您不必担心分配/取消分配或锁定/解锁或打开/关闭。

此外，它们使代码更简单，可读性更强。

编写上下文管理器有两种方法，要么创建自己的类，要么使用 *Contextlib* 模块创建上下文管理器装饰器。

让我们首先看看如何创建一个简单的**上下文管理器类**。一个上下文管理器类包含两个主要方法*进入*和*退出*。如果你熟悉测试，你可以用安装和拆卸来比较这两种方法。

就像 Python 中的每个类一样， *init* 方法是可选的。但是在上下文管理器的情况下，只有当我们使用带有 as 语句的*时，我们才使用 *init* 。 *init* 必须被传递你想要关联的名字，就像在 *with as* 语句中一样。*

现在让我们来看看一个简单的受《权力的游戏》启发的 ContextManager，它创建了一个代表房屋符号的*字典*。

就像 Python 中的每个类一样， *init* 方法只有在使用 *with *语句时才是可选的。如果你使用*with as* 语句，那么在 *with as* 语句中，它必须被传递你想要关联的名字。在 Python 中， *init* 方法只有在使用 *with *语句时才是可选的。如果你正在使用*with as* 语句，那么在 *with as* 语句中，必须向其传递你想要关联的名称。
类 ThronesContextManager:
def**init**(self，HouseSymbol):
self。HouseSymbol =房子符号

```
def __enter__(self):
    print("Enter: {}".format(self.HouseSymbol)")
    return self.HouseSymbol

def __exit__(self, *exc):
    print("Exit: {}".format(self.HouseSymbol)) 
```

以 ThronesContextManager({ " House Stark ":" Wolf " })为宅符:
宅符["坦格利安"] = "三头龙"

" " "
-输出-
进入:{ '史塔克家':'狼' }
退出:{ '史塔克家':'狼'，'坦格利安':'三头龙' }
" " "

```
 * The init method takes in the dict associated with the *as* in the *with-as* statement. It creates an instance of the class and assigns it to the dict. Much similar to any normal Python Class.
* The *enter* method is called by the *with* and is passed the dict. It returns the value which is associated with the dict(HouseSymbol).
*  The *exit* takes in the exception(*exc) these are of mainly three types exc: exception, exc_type: exception type and exc_tb: exception_traceback. 
*  If for some reason you want the program to ignore the exception you can also return True to just ignore the exception.

Now taking a look at the above code example we can say that any Context Manager is one which as two methods an *enter* method and an *exit* method. 

Before moving forward to *contextmanager decorator* let's break down the code snippit we saw in the starting of the post and see how it works behind the hood. 

Since we know how context managers work it won't be difficult to the observer what's happening when we call *with as* statement while opening a file.

```Python
with open('MyContextManager.txt') as f:
    f.write('Using Context Manager is FUN !') 
```

1.  *用*调用 File 类的**进入**方法。
2.  方法打开并返回文件。
3.  打开的文件句柄被传递给 *f* 。
4.  我们使用*写入文件。写()*。
5.  with 语句调用 **exit** 方法。
6.  出口检查异常，如果没有发现异常，它关闭文件。

编写上下文管理器更简单的方法是使用 **Contextlib** 模块并创建一个**上下文管理器装饰器**。

使用 **@contextmanager** 的好处是它可以自动为您构建进入和退出方法。因此，我们可以将生成器函数转换为 contextmanager 装饰器。

让我们再次重写 ThronesContextManager，但是使用一个 *@ThronesContextManager* 。

```
from contextlib import contextmanager

@contextmanager
def ThronesContextManager(data):
    print("Enter: {}".format(data))
    yield data 
    print("Exit: {}".format(data))

with ThronesContextManager({"House Stark": "Wolf"}) as HouseSymbol:
    HouseSymbol["Targaryen"] = "Three Headed Dragon"

"""
---Output---
Enter: {'House Stark': 'Wolf'}
Exit: {'House Stark': 'Wolf', 'Targaryen': 'Three Headed Dragon'}
""" 
```

#### 吡喃

这是我发现的一些关于 Contextmanagers 的有趣的事情。这些是我在研究这篇博文时发现的，因此我将它添加到 *PyRandom* 部分。随着我对 Contex 经理的了解越来越多，我会不断更新这一部分。

*   上下文管理器不会在程序中创建一个单独的新范围，即在*内定义的变量和*块一样，在块被执行后可用。

```
with open('MyContextManager.txt') as f:
    # Variable defined inside the Context Manager
    VariableName = f.read()
print(VariableName) 
```

*   当在带有语句的*中使用多上下文管理器时，*输入*和*退出*语句的流程变成 LIFO(后进先出)，即最后调用的输入方法将首先调用其退出方法。*

```
import contextlib

@contextlib.contextmanager
def make_context(name):
    print ('entering:', name)
    yield name
    print ('exiting :', name)

with make_context('A') as A, make_context('B') as B, make_context('C') as C:
    print ('inside with statement:', A, B, C)

"""
---OUTPUT---
entering: A
entering: B
entering: C
inside with statement: A B C
exiting : C
exiting : B
exiting : A
""" 
```

##### 现在怎么办？

既然我们已经介绍了关于上下文管理器的所有基本内容，我们可以开始深入研究，并学习在更真实的用例中使用上下文管理器。所以我想让你多读一些关于它的东西。

*   了解如何在上下文管理器中处理异常。
*   尝试找出最适合使用上下文管理器的真实项目用例。
*   在上下文管理器中找出 *init* 和 *enter* 的作用。

##### 还看不够？

博客背后的原因是我最近发现了一个 Python 问题，大概是这样的

编写一个名为 Suppress 的上下文管理器，它抑制给定类型的异常，即如果给定的异常类型被引发，该异常应该被捕获并在某种意义上被抑制。

代码示例:

```
>>> x = 0
>>> with suppress(ValueError):
...     x = int('hello')
...
>>> x
0
>>> with suppress(ValueError, TypeError):
...     x = int(None)
...
>>> x
0 
```

既然你读到这里，我想你也刚刚开始了解这个话题。让我们来测试一下到目前为止您所学到的知识，并获得一些编写上下文管理器的实践经验。试着解决这个问题。

我仍在解决这个问题，一旦完成，我会把我的解决方案链接到这里。

快乐编码😄