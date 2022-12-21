# [重写]链接:Python 中的双向别名

> 原文：<https://dev.to/circleoncircles/rewrite-link-bidirectional-aliasing-in-python-ekl>

Hihi~ Devs，

首先，我想告诉大家，Python-Ideas 是一个非常开放的地方，欢迎您的想法和贡献。

亲爱的参与讨论的人

> 这是我第一次来到 Python 核心开发者的地方，在几乎所有的[pep](https://www.python.org/dev/peps/)开始之前，在这里提出一个想法。这是一次非常有建设性的(当然也是技术性的)对话。考虑到我对核心语言的开发完全是新手，我对那些反应印象深刻。在 3 天的对话中，我学到了很多这门语言的技术方面。而且我知道还有很多要学习的！

4 天的讨论(线程)现在变得不活跃，很快就会被放弃。我想把这个想法留给使用任何编程语言的人，不仅限于 python。以下是我的重建提案摘要。原来的提议是[这里的](https://dev.to/circleoncircles/python-ideas-link-bidirectional-aliasing-in-python-3f20)和[线程](https://mail.python.org/archives/list/python-ideas@python.org/thread/NS5YFU3NLMZ63F3YWAHFKNXIWKHHKHRQ/)。

**Link** 是一种语言特性，它允许多个变量名总是引用一个名称空间中定义的同一个底层对象。

## 标注的环节

**链接**的符号甚至如何调用它都是开放的，可以修改。
现在，如果变量 a 与 b. I 链接，我将表示为`a >< b`或`link a, b`。`unlinks a`解除一切联系。要解除变量的链接，`unlink a`。**连杆**可通过`links()`检查，如`globals()`和`locals()`至`del`和`unlinks`，使用`del_and_unlinks a`。`@link(a,b)`作为函数链接参数的装饰器。要检查变量的链接，使用`aka(a)`。

### 链接可以被链接。

`a >< b >< c`与
相同

```
a >< b
b >< c 
```

## 链接行为

该行为可以简化为

```
a >< b
a = 1
print(b) # 1 b = 2
print(a) # 2 
del a
print(b) # will raise NamedError, both were un-binded. 
a = ['Cat', None]
assert a is b # True, both were rebinded. 
```

更复杂的场景

### 链接后有赋值

当一组名称是链接或链式链接时。以前只允许分配一个变量(或名称)。否则，链接将失败，并引发一个`TooManyAssignError`。因此，**链接**并不关心变量的顺序。

```
x = 5
x >< y # or `y >< x` would be ok 
a = 1
b = 'foo'
a >< b # raise TooManyAssignError 
```

### 解除链接后释放内存

```
a >< b >< c
a = HighMemObject()

del a # free memory at once 
b = HighMemObject()

unlinks b # unlink all but, all will keep referring to the `HighMemObject`. 
del a # b, c still refer to the `HighMemObject` del b # c still refer to the `HighMemObject` del c # free memory 
```

> **链接**或多或少有点像量子物理中的纠缠性质。

## 理

### 语言平滑转移【2020 年 11 月 3 日新增】

假设你来自另一种语言，比如 js。您可以通过“链接”将更快的过渡存档。我将在`String` - `str`
中提供一些例子

```
" Hi dude. ".trim() // "Hi dude."
"COVID-19 symptoms are Fever, Cough, and Shortness of breath".includes("Fever") // true 
```

```
str.trim >< str.strip
"    Hi dude.      ".trim() # "Hi dude." 
str.includes >< str.contains
"COVID-19 symptoms are Fever, Cough, and Shortness of breath".includes("Fever") // True 
```

### 在整个复杂过程中改变变量名

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

这也可能有利于一个在交互式环境中编码的人，比如`jupyter`、 **del once 和 free mem。无处不在**不需要跟踪同一个对象的变量。

```
import pandas as pd

df2train = pd.read_csv('train.csv') # VERY Large DataFrame df >< df2train 

...

df >< df_didEDA

...

df2train >< df_preprocessed

...

df >< df_trained
...

aka(df) # df, df2train, df_didEDA, df_preprocessed, df_trained 
del df # del once and free mem. everywhere! >//< 
```

### 简化走样

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

    def __init__(self, ...)
        ...
        self.uninstall = self.remove

# monkey patching PackageManager.uninstall = lambda x: print('uninstalled')
PackageManager.remove = lambda x: print('uninstalled') 
```

我们可以就

```
class PackageManager:
    uninstall >< remove
    installed >< packages
    packages = ['requests', 'loguru']

    def remove(): 
        ...

# Plus >//<, one-stop service monkey patching PackageManager.uninstall = lambda x: print('uninstalled') 
```

### 将非直观/非 pythonic 化的命名改为更好的名称

每个人都有不同的词汇。代码用户可能很难理解编码人员创造的某些不熟悉的词汇。链接变量允许它们在保持完全兼容性的同时更改为新名称。

```
from selenium import webdriver

driver = webdriver.Firefox()
webdriver.find_element_by_name >< webdriver.get_element_by_name

elem = driver.get_element_by_name("q") 
```

### 多语种代码

```
class Human:

    love >< 爱 >< любовь >< รัก >< 💞

    @link(you, вы, คุณ, 您)
    def love(self, you: 'Human'):
        self.soulmate = soulmate

    def code(self):
        return "import json"

    # link function name of similar term
    program >< code

# link class name of different languages link Human, มนุษย์, 人的, человек, 👤

me = человек()
assert me.program() == me.code() # True 

# monkey patch all at once! 人的.program = lambda x: "import this"
assert มนุษย์().program() == me.code() # True 
```

## [probablytoambitiousexception]允许对指定对象的一部分进行非嵌套命名

我不确定这是否应该是 ***链接*** 的一部分。因为不是所有的对象都可以赋值，所以样本应该是

```
house = {'resident': {'count': 12}}
house['resident']['number'] >< n_resident
n_resident = 3
print(house['resident']['number']) # print 3 
del_and_unlink house 
```

## 实现设想

关于它有几种讨论。具体的机理我掌握不了。这是清单

1.  名称空间的东西
2.  [*别名 _ 标识符*和设定行为](https://mail.python.org/archives/list/python-ideas@python.org/message/RQNPJZXOIGXQTM6CBSMS2MKBWA3QI6NH/)——理查德·穆西尔

总之，对我来说并不像看起来那么简单😂。还没有谈到性能，没有坚实的实现这一点。

# 同类现有

1.  指向同一地址的 C++指针`auto & a = b`
2.  c 预处理指令(`#define`)用于定义宏。
3.  Python 赋值/链式赋值:快照瞬间的同一个对象。

鸣谢:封面照片由文森特·范·扎林格在 Unsplash 上拍摄