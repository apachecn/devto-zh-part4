# 如何记住熊猫指数法

> 原文：<https://dev.to/discdiver/how-to-remember-pandas-index-methods-3l0d>

当方法名相似时，很难在头脑中将它们分开。这使得记住它们变得更加困难。

Pandas 有许多创建和调整数据帧索引的方法。这是一个简短的指南，帮助你在不同的记忆方法之间创造一点心理空间。

Jupyter 笔记本在 Kaggle [这里](https://www.kaggle.com/discdiver/how-to-remember-pandas-index-methods/)。

```
import pandas as pd
import numpy as np 
```

Enter fullscreen mode Exit fullscreen mode

### 制作一个数据帧，不指定索引(你得到一个默认索引)。

```
df = pd.DataFrame(dict(a=[1,2,3,4], b=[2,5,6,4]))
df 
```

Enter fullscreen mode Exit fullscreen mode

|  | a | b |
| --- | --- | --- |
| Zero | one | Two |
| one | Two | five |
| Two | three | six |
| three | four | four |

### 通过使用 [index](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) 关键字参数制作一个带索引的数据帧。

```
df2 = pd.DataFrame(dict(a=[1,2,3,4], b=[2,5,6,4]), index = [1,2,5,6])
df2 
```

Enter fullscreen mode Exit fullscreen mode

|  | a | b |
| --- | --- | --- |
| one | one | Two |
| Two | Two | five |
| five | three | six |
| six | four | four |

### 用[移动一列成为索引。set_index()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.set_index.html#pandas.DataFrame.set_index)

```
df3 = df2.set_index("a")
df3 
```

Enter fullscreen mode Exit fullscreen mode

|  | b |
| --- | --- |
| a |  |
| --- | --- |
| one | Two |
| Two | five |
| three | six |
| four | four |

### 用[重新命名指标值。索引](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Index.html)

```
df3.index = [2,3,4,5]
df3 
```

Enter fullscreen mode Exit fullscreen mode

|  | b |
| --- | --- |
| Two | Two |
| three | five |
| four | six |
| five | four |

注意`index`是数据帧的属性而不是方法，所以语法不同。

### 用[将指标值清零并从 0 重新开始。reset_index()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.reset_index.html#pandas.DataFrame.reset_index)

```
df4 = df3.reset_index()
df4 
```

Enter fullscreen mode Exit fullscreen mode

|  | 指数 | b |
| --- | --- | --- |
| Zero | Two | Two |
| one | three | five |
| Two | four | six |
| three | five | four |

如果不希望索引变成列，就将`drop=True`传递给`reset_index()`。

```
df5 = df3.reset_index(drop=True)
df5 
```

Enter fullscreen mode Exit fullscreen mode

|  | b |
| --- | --- |
| Zero | Two |
| one | five |
| Two | six |
| three | four |

### 用[对行重新排序。reindex()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.reindex.html#pandas.DataFrame.reindex)

```
df6 = df5.reindex([2,3,1,0])
df6 
```

Enter fullscreen mode Exit fullscreen mode

|  | b |
| --- | --- |
| Two | six |
| three | four |
| one | five |
| Zero | Two |

传递不在索引中的值会导致 NaN。

```
df7 = df5.reindex([2,3,1,0,6])
df7 
```

Enter fullscreen mode Exit fullscreen mode

|  | b |
| --- | --- |
| Two | Six |
| three | Four |
| one | Five |
| Zero | Two |
| six | 圆盘烤饼 |

## 建议

理想情况下，在用`index =`创建数据帧时添加一个索引。

如果从. csv 文件中读取，可以通过传递列号来设置索引列。

例如:

`df = pd.read_csv(my_csv, index_col=3)`

或者通过`index_col=False`来 exlcude。

## 如何设置或更改指标:

*   `df.set_index()` -将一列移动到索引中

*   `df.index` -手动添加索引

*   `df.reset_index()` -将索引重置为 *0，1，2...*

*   `df.reindex()` -对行重新排序

## 单词联想记忆:

*   `set_index()` -移动列

*   `index` -手动

*   `reset_index()` -复位

*   `reindex` -重新排序

## 换行

我希望这篇文章能帮助你创造一点思维空间来保持熊猫指数方法的正确性。如果是的话，请给它一些爱，这样其他人也能找到它。

我写关于数据科学、开发运营、Python 和其他东西的文章。如果我的其他[文章](https://medium.com/@jeffhale)听起来有趣的话，请查看一下。

关注我并连接:
[中](https://medium.com/@jeffhale)
[dev . to](https://dev.to/discdiver)
[Twitter](https://twitter.com/discdiver)
[LinkedIn](https://www.linkedin.com/in/-jeffhale)
[ka ggle](https://www.kaggle.com/discdiver)
[GitHub](https://github.com/discdiver)

[![Reset Button](img/401800c15cc3ceb8d3365dc1efe1294d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MwmIq3Ps--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gd6kiza5mgsh470nruhi.jpg)

索引快乐！