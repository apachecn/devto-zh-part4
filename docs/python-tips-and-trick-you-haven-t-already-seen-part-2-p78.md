# Python 技巧和诀窍，您还没有看过，第 2 部分。

> 原文：<https://dev.to/martinheinz/python-tips-and-trick-you-haven-t-already-seen-part-2-p78>

*注:这最初发布在[martinheinz . dev](https://martinheinz.dev/blog/4)T3】*

几周前，我发表了一篇文章([此处](https://dev.to/martinheinz/python-tips-and-trick-you-haven-t-already-seen-1p41))关于一些不太为人所知的 Python 特性，很多人似乎都喜欢它，所以这里有另一轮 Python 特性，希望你还没有看到。

## 使用`slice`功能命名切片

使用大量硬编码的索引值很快会导致维护和可读性方面的混乱。一种选择是对所有索引值使用常量，但是我们可以做得更好:

```
#              ID    First Name     Last Name line_record = "2        John         Smith"

ID = slice(0, 8)
FIRST_NAME = slice(9, 21)
LAST_NAME = slice(22, 27)

name = f"{line_record[FIRST_NAME].strip()}  {line_record[LAST_NAME].strip()}"
# name == "John Smith" 
```

在这个例子中我们可以看到，我们可以避免神秘的索引，首先使用`slice`函数命名它们，然后在切掉字符串的一部分时使用它们。您还可以使用切片对象的属性`.start`、`.stop`和`.step`来获得关于切片对象的更多信息。

## 运行时提示用户输入密码

许多命令行工具或脚本需要用户名和密码才能操作。因此，如果你碰巧编写了这样的程序，你可能会发现`getpass`模块很有用:

```
import getpass

user = getpass.getuser()
password = getpass.getpass()
# Do Stuff... 
```

这个非常简单的包允许你提示用户输入密码，并通过提取当前用户的登录名来获取他们的用户名。请注意，不是每个系统都支持隐藏密码。Python 会试图警告你这一点，所以只需在命令行中阅读警告。

## 查找单词/字符串的相近匹配项

现在，让我们来看看 Python 标准库的一些更难理解的特性。如果你发现自己处于这样的情况，你需要使用像[Levenshtein distance](https://en.wikipedia.org/wiki/Levenshtein_distance)Python 和`difflib`这样的东西来查找类似于某个输入字符串的单词。

```
import difflib
difflib.get_close_matches('appel', ['ape', 'apple', 'peach', 'puppy'], n=2)
# returns ['apple', 'ape'] 
```

`difflib.get_close_matches`找到最佳的*“足够好”*匹配。这里，第一个参数与第二个参数相匹配。我们还可以提供可选参数`n`,指定要返回的最大匹配数。另一个可用的关键字参数`cutoff`(默认为 0.6)可以设置为改变匹配字符串的得分阈值。

## 处理 IP 地址

如果你必须用 Python 做一些网络工作，你可能会发现`ipaddress`模块非常有用。一个用例是从 CIDR 生成 ip 地址列表(*无类域间路由* ):

```
import ipaddress
net = ipaddress.ip_network('74.125.227.0/29')  # Works for IPv6 too
# IPv4Network('74.125.227.0/29') 
for addr in net:
    print(addr)

# 74.125.227.0
# 74.125.227.1
# 74.125.227.2
# 74.125.227.3
# ... 
```

另一个很好的特性是 ip 地址的网络成员检查:

```
ip = ipaddress.ip_address("74.125.227.3")

ip in net
# True 
ip = ipaddress.ip_address("74.125.227.12")
ip in net
# False 
```

还有很多更有趣的功能我就不赘述了，因为你可以在这里找到那些[。但是请注意，在`ipaddress`模块和其他网络相关模块之间只有有限的互操作性。例如，您不能使用`IPv4Network`的实例作为地址字符串——它们需要首先使用`str`进行转换。](https://docs.python.org/3/howto/ipaddress.html)

## 调试程序在 Shell 中崩溃

如果你是一个拒绝使用 IDE 并且用 Vim 或 Emacs 编码的人，那么你可能会遇到这样一种情况，在 IDE 中使用调试器是很有用的。你知道吗？你有一个——用`python3.8 -i`运行你的程序——`-i`在你的程序终止时启动交互式 shell，从那里你可以探索所有变量并调用函数。不错，但是*实际*调试器(`pdb`)怎么样？

让我们使用下面的程序(`script.py` ):

```
def func():
    return 0 / 0

func() 
```

用`python3.8 -i script.py`和
运行脚本

```
# Script crashes... Traceback (most recent call last):
  File "script.py", line 4, in <module>
    func()
  File "script.py", line 2, in func
    return 0 / 0
ZeroDivisionError: division by zero
>>> import pdb
>>> pdb.pm()  # Post-mortem debugger > script.py(2)func()
-> return 0 / 0
(Pdb) 
```

我们看到了我们崩溃的地方，现在让我们设置一个断点:

```
def func():
    breakpoint()  # import pdb; pdb.set_trace()
    return 0 / 0

func() 
```

现在再运行一次:

```
script.py(3)func()
-> return 0 / 0
(Pdb)  # we start here (Pdb) step
ZeroDivisionError: division by zero
> script.py(3)func()
-> return 0 / 0
(Pdb) 
```

大多数情况下，打印语句和回溯对于调试来说已经足够了，但是有时，您需要开始探索，以了解程序内部发生了什么。在这些情况下，您可以设置断点，当您运行程序时，执行将在断点处停止，您可以检查您的程序，例如，列出函数参数、计算表达式、列出变量或如上所示单步执行。`pdb`是全功能的 python shell，所以你可以执行任何文学作品，但是你需要一些调试器命令，你可以在这里找到

## 在一个类中定义多个构造函数

一个在编程语言中很常见，但在 Python 中不常见的特性是函数重载。即使你不能重载普通函数，你仍然可以使用类方法重载构造函数:

```
import datetime

class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def today(cls):
        t = datetime.datetime.now()
        return cls(t.year, t.month, t.day) 
```

你可能会倾向于将备选构造函数的所有逻辑放入`__init__`中，并使用`*args`、`**kwargs`和一堆`if`语句来解决它，而不是使用类方法。这可能行得通，但可能会变得难以阅读和维护。因此，我建议在`__init__`中加入很少的逻辑，并在不同的方法/构造函数中执行所有的操作。通过这种方式，对于类的维护者和用户来说，你将得到干净清晰的代码。

## 使用装饰器缓存函数调用

您是否曾经编写过执行昂贵的 I/O 操作的函数，或者可以从缓存(内存化)其结果中获益的相当慢的递归函数？
如果你这样做了，那么使用`functools` :
中的`lru_cache`有一个简单的解决方案

```
from functools import lru_cache
import requests

@lru_cache(maxsize=32)
def get_with_cache(url):
    try:
        r = requests.get(url)
        return r.text
    except:
        return "Not Found"

for url in ["https://google.com/",
            "https://martinheinz.dev/",
            "https://reddit.com/",
            "https://google.com/",
            "https://dev.to/martinheinz",
            "https://google.com/"]:
    get_with_cache(url)

print(get_with_cache.cache_info())
# CacheInfo(hits=2, misses=4, maxsize=32, currsize=4) 
```

在这个例子中，我们正在缓存 *GET* 请求(最多 32 个缓存结果)。您还可以看到，我们可以使用`cache_info`方法检查函数的缓存信息。装饰器还提供了一个使缓存结果无效的`clear_cache`方法。我还想指出，这不应该用于有副作用的函数或每次调用都会创建可变对象的函数。

## 查找 Iterable 中出现频率最高的项目

在列表中找到最常见的条目是一项非常普通的任务，你可以使用`for`循环和字典(map)来完成，但是这将会浪费时间，因为在`collections`模块中有`Counter`类:

```
from collections import Counter

cheese = ["gouda", "brie", "feta", "cream cheese", "feta", "cheddar", 
          "parmesan", "parmesan", "cheddar", "mozzarella", "cheddar", "gouda",
          "parmesan", "camembert", "emmental", "camembert", "parmesan"]

cheese_count = Counter(cheese)
print(cheese_count.most_common(3))
# Prints: [('parmesan', 4), ('cheddar', 3), ('gouda', 2)] 
```

在引擎盖下，`Counter`只是一个将条目映射到出现次数的字典，因此你可以像普通的`dict` :
一样使用它

```
print(cheese_count["mozzarella"])
# Prints: 1 
cheese_count["mozzarella"] += 1 

print(cheese_count["mozzarella"])
# Prints: 2 
```

除此之外，您还可以使用`update(more_words)`方法轻松地向计数器添加更多元素。`Counter`的另一个很酷的特性是你可以使用数学运算(加法和减法)来组合和减去`Counter`的实例。

## 结论

我认为，如果你正在使用 Python，这次我在这里分享的大多数技巧几乎每天都有用，所以我希望它们能派上用场。此外，如果你对这些 Python 技巧和诀窍有任何想法，或者如果你知道解决上述问题的任何更好的方法，请告诉我！🙂