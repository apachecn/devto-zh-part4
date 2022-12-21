# 如何在 Python 中使用熊猫

> 原文：<https://dev.to/buzzedison/how-to-use-pandas-in-python-5ho1>

## 熊猫是什么？

Pandas 是一个简单而强大的库，用于数据操作和分析。熊猫这个名字来源于单词“Panel Data ”,所以不是熊猫这种动物

多维结构化数据集的术语。这只是一个超级有用的 Python 库的可爱名字！。熊猫在 numpy 上面跑。好吧，让我们来做个诱饵，谈谈 numpy。

### `What is Numpy?`

**(Numpy)** 数值 Python。它很快。它能比 python 列表或循环更好地处理数字。
NumPy 数组是 NumPy 库执行的所有计算的基础。它们是带有一些附加属性的简单 Python 列表。

### 有道理？

### 现在回到熊猫。

当谈到用 Python 分析数据时，Pandas 是一个相当大的游戏改变者，它是数据管理/争论中最受欢迎和广泛使用的工具之一，如果不是最常用的话。熊猫是由韦斯·麦金尼在 2015 年创建的，此后人们对 Python 进行数据挖掘和操作的兴趣越来越大。

### 熊猫的特征:

*   系列对象和数据框架
*   缺失数据的处理
*   数据调整
*   按功能分组
*   切片、索引和子设置
*   合并和加入
*   重塑
*   轴的分层标记
*   强大的输入/输出工具
*   时序特有的功能

### 为什么用熊猫而不仅仅是用 Numpy？

| 

#### Panda

 | 

#### NUMPY

 |
| 对于 500k 行或更多行，Pandas 的性能优于 Numpy | 对于 50k 或更少的行，性能更好 |
| Pandas 提供了丰富的时间序列功能、数据对齐、友好的统计、分组、合并和连接方法，以及许多其他便利 | NumPy 本身是一个相当低级的工具，与使用 MATLAB 非常相似 |

### 让我们开始实际工作吧。我们如何和熊猫一起工作？

当您想要使用 Pandas 进行数据分析时，您通常会以三种不同的方式使用它:

*   将 Python 的列表、字典或 Numpy 数组转换为 Pandas 数据框
*   使用熊猫打开一个本地文件，通常是一个 CSV 文件，但也可以是一个分隔文本文件(如 TSV)，Excel 等
*   通过 URL 打开网站上的远程文件或数据库，如 CSV 或 JSONon，或者从 SQL 表/数据库中读取

好的，那么我们如何使用它。我建议使用 Anaconda Python 发行版。
[https://www.anaconda.com/](https://www.anaconda.com/)

Anaconda 之所以受欢迎，是因为它一次安装就带来了许多数据科学和机器学习中使用的工具，因此它非常适合进行简短的设置。还附带 Numby，Jupyter 笔记本，熊猫，并安装了 Python 3。

我使用 Jupyter Notebook 作为数据科学的首选文本编辑器。我仍然喜欢 Visual Studio 代码，只是对于数据科学，你会情不自禁地爱上 Jupyter 和 anaconda 家族。

熊猫的数据集要么是一维的，要么是多维的

| 一维的 | 二维的 | 立体的 |
| 系列对象 | 数据帧 | 固定样本数据 |

让我们先处理系列对象。它是一个一维标签数组

要用熊猫，得先进口。

`import pandas as pd`
你需要有数据才能用它做任何事情。
所以让我们创建一个 python 列表。

`data = [1, 2, 3, 4]` #创建一个 python 列表

`series1 = pd.Series(data)` #将此列表转换成数据帧
`series1` 打印出结果。使用 Shift + Enter 或 jupyter 笔记本中的“运行”按钮来运行代码。

### 如何使用列表创建数据框架？

`general = ['Eddy', 'Bob', 'Linda', 'Ella']` #Python 列表
`generald = pd.DataFrame(general) `#creating the dataframe. pd.DataFrame not pd.dataframe.``

### `general `# print result``

好了，我要回去工作了，停下来整理一下。希望这有所帮助。如果你有任何问题或意见，请告诉我。

享受你剩下的一周吧