# 熊猫聚合的内嵌重命名

> 原文：<https://dev.to/rpm4real/in-line-renaming-of-pandas-aggregates-3i6n>

# 问题

在 pandas 中使用聚合数据框架时，我发现自己对聚合列的结果命名方式感到沮丧。默认情况下，它们继承您正在聚合的列的名称。例如，

```
import pandas as pd 
import numpy as np

iris = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv')

iris.groupby('species').agg({
    'sepal_length': np.mean
}).round(2) 
```

|  | 萼片长度 |
| --- | --- |
| 种类 |  |
| --- | --- |
| 感觉很好 | Five point zero one |
| 杂色的 | Five point nine four |
| virginica | Six point five nine |

所以很明显，我们作为上面代码的作者知道我们取了萼片长度的平均值。但是只看输出，我们不知道对萼片长度值做了什么。如果我们将聚合函数放在一个列表中，就可以解决这个问题:

```
iris.groupby('species').agg({
    'sepal_length': [np.mean]
}).round(2) 
```

|  | 萼片长度 |
| --- | --- |
|  | 意思是 |
| --- | --- |
| 种类 |  |
| --- | --- |
| 感觉很好 | Five point zero one |
| 杂色的 | Five point nine four |
| virginica | Six point five nine |

Pandas 添加了一行(技术上是添加了一个级别，创建了一个 multiIndex)来告诉我们应用于该列的不同聚合函数。在这种情况下，我们只应用了一个，但是您可以看到它如何适用于多个聚合表达式。

这种方法效果很好。如果您想要折叠 multiIndex 以创建更多可访问的列，您可以利用串联方法，这种方法受到了 [this stack overflow post](https://stackoverflow.com/questions/14507794/pandas-how-to-flatten-a-hierarchical-index-in-columns) 的启发(注意，其他实现也类似地使用`.ravel()` ):

```
df = iris.groupby('species').agg({
    'sepal_length': [np.mean]
}).round(2)
df.columns = ['_'.join(gp) for gp in df.columns.values]
df 
```

|  | 萼片长度平均值 |
| --- | --- |
| 种类 |  |
| --- | --- |
| 感觉很好 | Five point zero one |
| 杂色的 | Five point nine four |
| virginica | Six point five nine |

这两种解决方案都有一些紧迫的问题:

*   列名仍然可能与可读的英语相差甚远；
*   串联方法可能不适用于所有应用；
*   Pandas 采用任何自定义函数的`__name__`属性，并在这里将其用作列名。在使用自定义函数或 lambda 函数进行聚合的情况下，列名在这些格式中不太可能有意义。

# 另辟蹊径

我们可以利用`__name__`属性创建一个更清晰的列名，甚至是一个其他人也能理解的列名。👍

明确地说:我们显然可以在数据帧返回后重命名这些列，但是在这种情况下，我需要一个可以动态设置列名的解决方案。

## [T2 利用`__name__`属性](#taking-advantage-of-the-raw-name-endraw-attribute)

如果你不熟悉的话，`__name__`属性是你或其他人在 python 中定义的每个函数都附带的。

```
def this_function():
    pass 

print(this_function.__name__) 
```

> `this_function`

我们可以在定义该属性后对其进行更改:

```
def this_function():
    pass 

this_function.__name__ = 'that.'

print(this_function.__name__) 
```

> `that.`

当您使用 decorators 定义函数时，还有一些很好的选项来调整函数`__name__`。更多关于那个[这里](https://stackoverflow.com/questions/10874432/possible-to-change-function-name-in-definition)。

回到我们的应用程序，让我们检查以下情况:

```
def my_agg(x): 
    return (x/20).sum()

iris.groupby('species').agg({
    'sepal_length': [my_agg],
    'sepal_width': [my_agg]
}).round(2) 
```

|  | 萼片长度 | 萼片宽度 |
| --- | --- | --- |
|  | 我的 _agg | 我的 _agg |
| --- | --- | --- |
| 种类 |  |  |
| --- | --- | --- |
| 感觉很好 | Twelve point five two | Eight point five seven |
| 杂色的 | Fourteen point eight four | Six point nine two |
| virginica | Sixteen point four seven | Seven point four four |

在开始聚合之前，我们可以添加一行来调整`my_agg()`的`__name__`。但是如果我们可以在聚集的时候重命名函数呢？类似于我们在定义 SQL 语句中的列时重命名它们的方式。

## 高阶重命名功能

为了解决这个问题，我们可以定义一个高阶函数，它返回原始函数的副本，但是 name 属性发生了变化。看起来是这样的:

```
def renamer(agg_func,desired_name):
    def return_func(x):
        return agg_func(x)
    return_func.__name__ = desired_name
    return return_func 
```

我们可以在应用程序`my_agg`之外应用这个函数来动态重置`__name__`:

```
iris.groupby('species').agg({
    'sepal_length': [renamer(my_agg,'Cool Name')],
    'sepal_width': [renamer(my_agg,'Better Name')]
}).round(2) 
```

|  | 萼片长度 | 萼片宽度 |
| --- | --- | --- |
|  | 很酷的名字 | 更好的名字 |
| --- | --- | --- |
| 种类 |  |  |
| --- | --- | --- |
| 感觉很好 | Twelve point five two | Eight point five seven |
| 杂色的 | Fourteen point eight four | Six point nine two |
| virginica | Sixteen point four seven | Seven point four four |

## 现实的例子

这里有一个利用这个解决方案的完美场景:

```
from numpy import percentile

iris.groupby('species').agg({
    'sepal_length': [renamer(lambda x: percentile(x,25),'25th Percentile')],
    'sepal_width': [renamer(lambda x: percentile(x,75),'75th Percentile')]
}).round(2) 
```

|  | 萼片长度 | 萼片宽度 |
| --- | --- | --- |
|  | 第 25 百分位 | 第 75 百分位 |
| --- | --- | --- |
| 种类 |  |  |
| --- | --- | --- |
| 感觉很好 | Four point eight | Three point six eight |
| 杂色的 | Five point six | Three |
| virginica | Six point two two | Three point one eight |

为了获得萼片宽度和长度的各种百分位数，我们可以利用 lambda 函数，而不必费心定义我们自己的函数。我们使用重命名器给这些 lambda 函数起一个容易理解的名字。

为了更进一步，我们可以在重命名字符串中包含列名，并删除顶层的列 multiIndex:

```
from numpy import percentile

df3 = iris.groupby('species').agg({
    'sepal_length': [renamer(lambda x: percentile(x,25),'Length 25th Percentile')],
    'sepal_width': [renamer(lambda x: percentile(x,75),'Width 75th Percentile')]
}).round(2) 

df3.columns = df3.columns.droplevel()

df3 
```

|  | 长度第 25 百分位 | 第 75 百分位宽度 |
| --- | --- | --- |
| 种类 |  |  |
| --- | --- | --- |
| 感觉很好 | Four point eight | Three point six eight |
| 杂色的 | Five point six | Three |
| virginica | Six point two two | Three point one eight |

# 最后的想法

在处理熊猫数据帧时，有很多方法可以给猫剥皮，但我一直在寻找简化和加快工作流程的方法。这个解决方案帮助我完成了聚合步骤，并轻松地创建了可共享的表。它当然不会在所有情况下都有效，但是下次当您对无用的列名感到沮丧时，可以考虑使用它！