# [Python-Ideas]链接:Python 中的双向别名

> 原文：<https://dev.to/circleoncircles/python-ideas-link-bidirectional-aliasing-in-python-3f20>

**更新 2019 年 9 月 26 日**:我在[这里](https://dev.to/circleoncircles/rewrite-link-bidirectional-aliasing-in-python-ekl)的一篇新文章中更新并重写了这份提案。

> 我不擅长英语措辞。我会尽力传达这个想法。

Link 是一种语言特性，它允许多个变量名总是引用一个名称空间中定义的同一个底层对象。

就目前而言，如果变量 a 与 b. I 链接，将表示为 a > < b or link('a', 'b')

```
a = 2
a >< alpha # link 'a' with 'alpha' b = 3
print(b**alpha) # print 9 alpha = 3
print(b**a) # print 27 

class Human:
    def code(self):
        return "import json"

    # link function name of similar term
    program >< code

# link class name of different languages link('Human','มนุษย์', '人的', 'человек', '👤')

me = человек()
assert me.program() == me.code() # True 

# reassign function 人的.program = lambda x: "import this"
assert มนุษย์().program() == me.code() # True 
```

# 好人

## 在整个复杂过程中改变变量名

更容易理解。如果能再加一个有意义的名字。减少编码者和读者的认知负担。我们依靠更少的评论来跟踪事情。

> 我编码我的意思，我编码的意思。

```
students = School.list_students(year=4)
School.gruaduate(students)
students_graduated >< to_be_sentCertificates >< students

...

School.sendCertificates(students_graduated) 
```

> 允许片段/部分读取，无需跟踪上层变量的变化。

这也可能有利于在交互环境中编码的人，比如`jupyter`、**显式非复制赋值**、

```
import pandas as pd

df_train = pd.read_csv('train.csv')
df >< df_train # avoid copy, ensure always the same DataFrame df['meme_length'].hist() 
```

## 简化走样

而不是像这样用`@property`和`@foo.setter`修饰和定义在`__init__`里。

```
class PackageManager:
    packages = ['requests', 'loguru']

    @property
    def installed(self):
        return self.packages

    @installed.setter
    def installed(self, value):
        self.packages = value

    def remove(self):
        ...

    def __init__(self)
        ...
        self.uninstall = self.remove 
```

我们可以就

```
class PackageManager:
    packages = ['requests', 'loguru']
    installed >< packages

    def remove(): 
        ...
    uninstall >< remove 
```

## 将非直观/非 pythonic 化的命名改为更好的名称

每个人都有不同的词汇。代码用户可能很难理解编码人员创造的某些不熟悉的词汇。链接变量允许它们在保持完全兼容性的同时更改为新名称。

```
from selenium import webdriver

driver = webdriver.Firefox()
webdriver.find_element_by_name >< webdriver.search_element_using_name

elem = driver.search_element_using_name("q") 
```

## * * *允许对指定对象的一部分进行非嵌套命名

我不确定这是否应该是 ***链接*** 的一部分。因为不是所有的对象都可以赋值，所以样本应该是

```
house = {'resident_num': 2}
house['resident_num'] >< n_resident
n_resident = 3
print(house['resident_num']) # print 3 
```

## 追随‘蟒蛇之禅’

1.  简单比复杂好。
2.  扁平的比嵌套的好。
3.  名称空间是一个非常棒的想法——让我们多做一些吧！

# 坏了

## 构思新颖，无前人实现可借鉴

我在任何编程语言中都没有看到这样的特性。但是存在于许多人类语言中。

## 不遵循‘蟒之禅’

1.  面对暧昧，拒绝猜测的诱惑。
2.  应该有一种——最好只有一种——显而易见的方法来做这件事。

# 丑陋

## 需要一个系统来帮助个人编码员读/写代码

假设两个来自不同国家的人用不同的语言编写相同的脚本；英语和俄语。虽然它们都明确指定了链接变量。他们很快就会头晕，因为当他们遇到外国文本时，他们不得不来回看很多次。应该有一个系统来帮助他们选择默认的语言/词汇，这样他们就可以理解代码，就好像代码完全是由他们编写的一样。

# 同类现有

1.  指向同一地址的指针
2.  c 预处理指令(`#define`)用于定义宏。
3.  Bash 锯齿:单向锯齿
4.  Python 赋值/链式赋值:瞬间相同的值。

我想知道你们的想法。会对你的日常编码产生正面/负面的影响吗？