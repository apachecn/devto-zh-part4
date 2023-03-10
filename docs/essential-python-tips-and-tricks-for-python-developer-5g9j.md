# Python 开发人员必备的 Python 技巧和诀窍

> 原文：<https://dev.to/tuantvk/essential-python-tips-and-tricks-for-python-developer-5g9j>

# 用 python 写速记语句

Python 有速记语句和速记操作符。这些东西将帮助你用更少的语句写出更多的逻辑。

我们将看到那些可用的速记语句。

> 文件[示例速记. py](https://github.com/tuantvk/python-cheatsheet/blob/master/src/shorthand-in-python/shorthand.py)

## λ语句

可能每个人都知道λ函数。lambda 语句有助于在不命名函数的情况下编写单行函数。这将返回函数引用，您可以将它赋给任意变量。更像是 JavaScript 匿名函数。

```
foo = lambda a: a+3
foo(3)
6
foo(8)
11 
```

Enter fullscreen mode Exit fullscreen mode

## 自我称为λ

你可以编写 lambda，让它像 javascript 中的自调用函数一样调用自己。让我们看一个例子。

```
(lambda a: a+3)(8)
11
(lambda x: x**x)(3)
27 
```

Enter fullscreen mode Exit fullscreen mode

## 列表理解

列表理解是 python 的一大特色。使用这个特性可以减少代码量，可以降低代码的空间复杂度。简单的 for 循环可以使用列表理解来编写。

> 语法:
> 
> **L** = **[** 映射-表达式**用于** source-list **if** 中的元素
> 
> 其中:
> 
> **L** 变量，结果赋值给
> 
> **映射-表达式**表达式，仅当 if 条件中的过滤器-表达式>解析为真时，在每个循环中执行
> 
> 这个列表理解相当于。

```
result = []
for element in source-list:
  if filter-expression:
    result.append(mapping-expression) 
```

Enter fullscreen mode Exit fullscreen mode

### 举例

让我们看看列表理解示例。从给定范围中获取偶数。

*   常用代码

```
result = []
for i in range(10):
  if i%2 == 0:
    result.append(i)

print(result)
[0, 2, 4, 6, 8] 
```

Enter fullscreen mode Exit fullscreen mode

*   列表理解

```
[i for i in range(10) if i%2==0]
[0, 2, 4, 6, 8] 
```

Enter fullscreen mode Exit fullscreen mode

## 字典理解

python 2.7 和 3.x 中提供了 Dict comprehension。这种语法将为您提供一种方法，将用于创建字典的几行代码封装成一行。它类似于列表理解，但是我们使用字典文字{}而不是[]。

> 语法:
> 
> {**key**:if**filter-expression**中**元素**的**值**

让我们通过一个例子来说明如何使用它。

我有一个水果清单，我想通过改变它们的大小写来把它变成字典

['苹果'，'芒果'，'橘子']

我想把所有的键都转换成小写。这是我们不用理解就能做到的。

```
l = ['MANGO', 'APPLE', 'ORANGE']

d = {}

for i in l:
  d[i.upper()] = 1

{'ORANGE': 1, 'MANGO': 1, 'APPLE': 1} 
```

Enter fullscreen mode Exit fullscreen mode

使用简单的列表理解。

```
{i.upper(): 1 for i in l} 
```

Enter fullscreen mode Exit fullscreen mode

## 设定领悟

Set comprehension 语法与 dict comprehension 非常相似，但有一点不同。

让我们考虑一下字典理解的例子。使用以下语句生成集合

```
{i.upper() for i in l} 
```

Enter fullscreen mode Exit fullscreen mode

我们没有像在字典理解中那样指定值

## 生成器表达式

你可能已经知道发电机了。任何包含产出陈述的函数都称为生成元。生成器给出了 iterable，在这里我们可以调用 next 方法来获取序列中的下一项。Python 为这个生成器取了一个简短的符号，比如 lambda。这和列表理解是一样的，但是我们用多种文字将表达式括起来。

*   发电机功能

```
def gen():
  for i in range(10):
    yield i 
g = gen()
<generator object gen at 0x7f60fa104410>
g.next()
0
g.next()
1 
```

Enter fullscreen mode Exit fullscreen mode

*   生成器表达式

相同的生成器函数可以编写如下。

```
g = (i for i in range(10))
g
<generator object <genexpr> at 0x7f60fa1045f0>
g.next()
0 
```

Enter fullscreen mode Exit fullscreen mode

## 速记 If Else

像 C 和 javascript 三元运算符(？:)可以写简写 if-else 比较。考虑到可读性，我们在 python 中有以下语法

**if-expression** if **(条件)** else **else-expression**

这相当于。

```
if True:
  print("This is True")
else:
  print("This is False") 
```

Enter fullscreen mode Exit fullscreen mode

## 元组解包

Python 3 更强大的解包功能。在这里。

示例:

```
a, rest = [1, 3, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，a 将得到 1，列表的其余部分将被赋给变量 rest。即[3，4，6]

## 带分隔符的字符串串联

如果你想用一些随机分隔符连接字符串列表。你可以通过使用字符串方法**连接**
来实现

```
" || ".join(["hello", "world", "how", "are", "you"])

'hello || world || how || are || you' 
```

Enter fullscreen mode Exit fullscreen mode

## 强大的俏皮话

您是否厌倦了通读一行行代码并迷失在条件语句中？Python 一行程序可能正是您正在寻找的。例如，条件语句。

```
if alpha > 7:
   beta = 999
elif alpha == 7:
   beta = 99
else:
   beta = 0 
```

Enter fullscreen mode Exit fullscreen mode

真的可以简化为:

```
beta = 999 if alpha > 7 else 99 if alpha == 7 else 0 
```

Enter fullscreen mode Exit fullscreen mode

## 从列表中删除重复项

大多数时候，我们希望从列表中删除或找到重复的项目。让我们看看如何从列表中删除重复项。最好的方法是将列表转换成集合。集合是唯一值的无序数据结构，不允许复制。

```
listNumbers = [20, 22, 24, 26, 28, 28, 20, 30, 24]
print("Original= ", listNumbers)

listNumbers = list(set(listNumbers))
print("After removing duplicate= ", listNumbers) 
```

Enter fullscreen mode Exit fullscreen mode

## 如何高效比较两个无序列表

以上两个列表包含相同的元素，只是顺序不同。让我们看看如何找到两个相同的列表。

*   如果我们的对象是可散列的，我们可以使用`collections.Counter`方法。
*   如果对象是可排序的，我们可以使用`sorted()`。

```
from collections import Counter

one = [33, 22, 11, 44, 55]
two = [22, 11, 44, 55, 33]
print("is two list are b equal", Counter(one) == Counter(two)) 
```

Enter fullscreen mode Exit fullscreen mode

## 将字节转换成字符串

要将字节转换为字符串，我们可以解码 bytes 对象以产生一个字符串。你可以用你想要的字符集解码。

```
byteVar = b"pynative"
str = str(byteVar.decode("utf-8"))
print("Byte to string is" , str ) 
```

Enter fullscreen mode Exit fullscreen mode

## 将十六进制字符串转换为 int 字符串

```
hexNumber = "0xfde"
stringNumber="34"

print("Hext toint", int(hexNumber, 0))
print("String to int", int(stringNumber, 0)) 
```

Enter fullscreen mode Exit fullscreen mode

### 推荐

*   **教程**

    *   [学习 Python | CodeAcademy](https://www.codecademy.com/learn/learn-python)
    *   [Progate Python 类](https://progate.com/languages/python)
    *   [绝对初学者视频教程| YouTube](http://bit.ly/2NkrsKh)
    *   [Python 简介| Udacity](https://in.udacity.com/course/introduction-to-python--ud1110-india) 🆓
    *   [Python 给大家看](https://www.coursera.org/specializations/python)
    *   [写出更好的 Python 函数](https://jeffknupp.com/)
    *   [学习 Python:从零到英雄](https://medium.freecodecamp.org/learning-python-from-zero-to-hero-120ea540b567)
    *   [用 Python 自动化枯燥的东西](https://automatetheboringstuff.com/) -推荐
    *   [新波士顿 Python | Youtube](https://www.youtube.com/playlist?list=PL6gx4Cwl9DGAcbMi1sH6oAMk4JHw91mC_)
    *   [想 Python 2e -绿茶出版社](http://greenteapress.com/thinkpython2/thinkpython2.pdf)
    *   [一个字节的 Python](https://python.swaroopch.com/)
    *   [项目欧拉](https://projecteuler.net/)
    *   [Python 旋风之旅](https://github.com/jakevdp/WhirlwindTourOfPython)
    *   [Python 数据科学手册](https://github.com/jakevdp/PythonDataScienceHandbook)
    *   [谷歌的 Python 类](https://developers.google.com/edu/python/)——推荐
    *   [数据科学 Python 简介](https://www.datacamp.com/courses/intro-to-python-for-data-science)
    *   [Python 3 适合希望接触实际项目的人](https://pythonprogramming.net/)
    *   [艰难地学习 Python](https://learnpythonthehardway.org/)
*   **Django - Python**

    *   试试 Django | YouTube
    *   [Django 文档](https://docs.djangoproject.com/en/2.1/)
    *   [姜戈女孩](https://tutorial.djangogirls.org/en/)
    *   [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django)
    *   [SimpleIsBetterThanComplex Blog](https://simpleisbetterthancomplex.com/)
    *   [探戈与姜戈书](https://www.tangowithdjango.com/book/)
    *   [Django 基于类的视图](https://ccbv.co.uk/)
    *   [算法 Python](https://github.com/TheAlgorithms/Python)
*   **Flask - Python**

    *   [烧瓶巨型教程](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)

🎉 🎉 🎉

### [在 Github 上查看我的回购](https://github.com/tuantvk/python-cheatsheet)💡