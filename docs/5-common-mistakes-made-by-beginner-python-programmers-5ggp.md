# 初级 Python 程序员常犯的 5 个错误

> 原文：<https://dev.to/anuragrana/5-common-mistakes-made-by-beginner-python-programmers-5ggp>

最初发表于[pythoncircle.com](https://www.pythoncircle.com/post/602/5-common-mistakes-made-by-beginner-python-programmers/)

在作为 python 程序员的最初几天，我们所有人都面临着代码中某种类型的奇怪错误，在 StackOverflow 上花费了几个小时的痛苦时间后，结果证明这不是一个错误，而是 python 的一个特性。这就是 python 的工作方式。下面是大多数初学 python 的程序员最常犯的 5 个错误。让我们了解一下他们，这样我们就可以节省几个小时在脸书页面和群组上提问的时间。

**-创建字典或列表的副本。**

每当您需要制作字典或列表的副本时，不要简单地使用赋值操作符。

错误:

```
>>> dict_a = {"name": "John", "address":"221B Baker street"}
>>> dict_b = dict_a 
```

现在如果你编辑/更新`dict_b`，`dict_a`也将被更新，因为通过使用赋值操作符，你试图说`dict_b`将指向`dict_a`所指向的同一个对象。

```
>>> dict_b["age"] = 26
>>> dict_b
{'address': '221B Baker street', 'name': 'John', 'age': 26}
>>> dict_a
{'address': '221B Baker street', 'name': 'John', 'age': 26}
>>> 
```

正确:

使用`copy()`或`deepcopy()`方法。

```
>>> dict_c = dict_b.copy()
>>> dict_c["location"] = "somewhere"
>>> dict_c
{'address': '221B Baker street', 'name': 'John', 'age': 26, 'location': 'somewhere'}
>>> dict_b
{'address': '221B Baker street', 'name': 'John', 'age': 26}
>>> dict_a
{'address': '221B Baker street', 'name': 'John', 'age': 26}
>>> 
```

[看 copy 和 deepcopy 的区别。](https://www.pythoncircle.com/post/249/difference-between-list-and-tuples-in-python-and-more-such-comparisons/)

**-字典键。**

假设我们将下面的值放在一个字典中。

```
>>> dict_a = dict()
>>> dict_a
{}
>>> dict_a[1] = "apple"
>>> dict_a[True] = "mango"
>>> dict_a[2] = "melon" 
```

如果我们尝试打印字典，输出会是什么。让我们看看。

```
>>> dict_a
{1: 'mango', 2: 'melon'} 
```

刚刚发生了什么？钥匙`True`在哪里？
记住布尔类是整数的子类。`True`的整数当量为 1，`False`的整数当量为 0。因此键 1 的值被覆盖。

```
>>> isinstance(True, int)
True
>>> isinstance(False, int)
True
>>> True == 1
True
>>> False == 0
True
>>> 
```

**-更新列表或字典。**
假设您想要在列表中添加一个项目。

```
>>> list_a = [1,2,3,4,5]
>>> list_a = list_a.append(6)
>>> list_a
>>> # prints nothing 
```

尝试更新字典。

```
>>> dict_a = {"a" : "b"}
>>> dict_a = dict_a.update({"c" : "d"})
>>> dict_a
>>> # prints nothing 
```

好，我们来试着排序一个列表。

```
>>> list_b = [2,5,3,1,7]
>>> list_b = list_b.sort()
>>> list_b
>>> # prints nothing 
```

为什么什么都没印出来？我们做错了什么？

大多数顺序对象方法，如排序、更新、追加、添加等，通过避免创建不必要的单独副本来提高性能。

不要试图将此类方法的输出赋给变量。

正确的方式:

```
>>> list_a = [1,2,3,4,5]
>>> list_a.append(6)
>>> dict_a = {"a" : "b"}
>>> dict_a.update({"c" : "d"})
>>> dict_a
{'c': 'd', 'a': 'b'}
>>> list_a.sort()
>>> list_a
[1, 2, 3, 4, 5, 6] 
```

**-被拘留的字符串。**
在某些情况下，python 试图重用现有的不可变对象。字符串实习就是这样一个例子。

```
>>> a = "gmail"
>>> b = "gmail"
>>> a is b
True 
```

这里我们试图创建两个不同的字符串对象。但是当检查两个对象是否相同时，它返回 True。这是因为 python 没有创建另一个对象`b`，而是将`b`指向第一个值“gmail”。

所有长度为 1 的字符串都被保留。除 ASCII 字符、数字和下划线之外的任何字符串都不会被保留。

让我们看看。

```
>>> a = "@gmail"
>>> b = "@gmail"
>>> a is b
False
>>> 
```

另外，记住`==`不同于`is`操作符。`==`检查值是否相等，而`is`检查两个变量是否指向同一个对象。

```
>>> a = "@gmail"
>>> b = "@gmail"
>>> a is b
False
>>> a == b
True
>>> 
```

因此，在使用不可变字符串或`==`或`is`操作符时，请记住上述要点。

**-默认参数只计算一次。**

考虑下面的例子。

```
def func(a, lst=[]):
    lst.append(a)
    return lst

print(func(1))
print(func(2)) 
```

你认为上面两条打印语句的输出会是什么？

让我们试着运行它。

```
>>> def func(a, lst=[]):
...     lst.append(a)
...     return lst
... 
>>> print(func(1))
[1]
>>> print(func(2))
[1, 2]
>>> 
```

为什么第二种情况输出是`[1,2]`。不应该只是`[2]`吗？

所以这里的问题是，函数的默认参数只计算一次。在第一次调用时，即`func(1)`，列表`lst`被评估并被发现为空，因此向其追加 1。但是在第二次调用时，列表已经有了一个元素，因此输出是[1，2]

**奖励:**不要混用空格和制表符。就是不要。你会哭。

更从[**pythoncircle.com**](https://www.pythoncircle.com):

*   [添加机器人。Django 应用程序的 Txt 文件](https://www.pythoncircle.com/post/578/adding-robotstxt-file-to-django-application/)
*   [**如何在 Pythonanywhere 上免费托管 Django App**](https://www.pythoncircle.com/post/18/how-to-host-django-app-on-pythonanywhere-for-free/)
*   [如何使用 Office 365 从 Python 和 Django 发送电子邮件](https://www.pythoncircle.com/post/36/how-to-send-email-from-python-and-django-using-office-365/)