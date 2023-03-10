# Python 熊猫，数据处理

> 原文：<https://dev.to/petercour/python-pandas-data-processing-12in>

pandas 模块让你解析数据。例如，您可以拥有想要读取的 excel 数据。

[![excel](img/ef68612eab0072052231cbbe6f5a7f2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aWJnjL9Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cuull7cpgso7cg9xwgtv.png)

您可以使用 read_excel(filename)方法加载 excel 文件，其中 filename 可以包含路径。它可以读取 xls 和 xlsx。

该数据存储在数据框中。数据框是 pandas 中的一个数据结构，可以编辑或者[出图](https://pythonbasics.org/seaborn_pandas/)。

```
#!/usr/bin/python3
# coding: utf-8
import  pandas  as pd

df = pd.read_excel('example.xls')
data1 = df.head(7)
data2 = df.values
print("A \n{0}".format(data1))
print("B \n{0}".format(data2)) 
```

我们在这里做的事情非常简单。我来描述一下步骤。

[![](img/e9ea2e2a2516edc11c7c45c4e356bb56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yxpPScVR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FdPbpVcfgGldBu%252Fgiphy.gif%26f%3D1)

加载 excel 数据。该文件必须在同一目录中，否则必须指定路径。

```
df = pd.read_excel('example.xls') 
```

将数据框中的数据存储到变量中

```
data1 = df.head(7)
data2 = df.values 
```

输出这些变量。因为它不是一个单一的值，我们把它格式化。

```
print("A \n{0}".format(data1))
print("B \n{0}".format(data2)) 
```

在终端中运行它(如果您愿意，也可以在 IDE 中运行)

```
python3 example.py 
```

从 excel 输出数据:

```
A 
       id    name  class       date  stature
0  201901   Aaron      1 2019-01-01        1
1  201902  Arthur      1 2019-01-02        1
2  201903   Angus      1 2019-01-03        1
3  201904  Albert      2 2019-01-04        2
4  201905  Adrian      2 2019-01-05        2
5  201906    Adam      3 2019-01-06        1
6  201907  Andres      3 2019-01-07        1
B 
[[201901 'Aaron' 1 Timestamp('2019-01-01 00:00:00') 1]
 [201902 'Arthur' 1 Timestamp('2019-01-02 00:00:00') 1]
 [201903 'Angus' 1 Timestamp('2019-01-03 00:00:00') 1]
 [201904 'Albert' 2 Timestamp('2019-01-04 00:00:00') 2]
 [201905 'Adrian' 2 Timestamp('2019-01-05 00:00:00') 2]
 [201906 'Adam' 3 Timestamp('2019-01-06 00:00:00') 1]
 [201907 'Andres' 3 Timestamp('2019-01-07 00:00:00') 1]
 [201908 'Alex' 3 Timestamp('2019-01-08 00:00:00') 1]] 
```

相关链接:

*   [熊猫模块](https://pandas.pydata.org/)
*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)