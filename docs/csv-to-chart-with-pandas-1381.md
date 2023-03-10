# Csv 将与熊猫一起制作图表

> 原文：<https://dev.to/petercour/csv-to-chart-with-pandas-1381>

任何计算机都一直在使用数据。有时在数据库中，有时在网络上，有时来自感官输入，有时来自办公室数据，如 excel 或 csv。

所以你可能知道你可以很容易地用熊猫解析 csv 文件。但是您知道吗，您可以非常容易地直接从 csv 数据创建图表。

### 数据集

csv 数据集就是数据。它可能来自 GSheets 或 Open Office 等办公套件。您可以将文件保存为 csv 格式，用逗号分隔。顾名思义，每个值都用逗号分隔。

任何数据集都可以，但是下面的例子使用了这个 csv 数据集。

[![](img/da9af294e3622e5a5f16850bebfde974.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ItjL2joY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F3o7rc0qU6m5hneMsuc%252Fgiphy.gif%26f%3D1)

这个数据集是关于电影的。对于每部电影，它都会保存这些值:

*   军阶
*   标题
*   类型
*   描述
*   导演
*   演员
*   年
*   运行时间(分钟)
*   评级
*   投票
*   收入(百万)
*   Metascore

所以这是大量的信息。这是一个 1000 条记录的小数据集。

### 熊猫

我们首先加载 pandas 模块，matplotlib 用于绘图，numpy 用于数字处理。然后使用 matplotlib 绘制数据。加载 csv 数据并创建图形。

```
#!/usr/bin/python3
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

movie = pd.read_csv("IMDB-Movie-Data.csv")

movie["Rating"].mean()
movie["Rating"].plot(kind="hist", figsize=(20, 8))

plt.figure(figsize=(20, 8), dpi=80)
plt.hist(movie["Rating"], 20)
plt.xticks(np.linspace(movie["Rating"].min(), movie["Rating"].max(), 21))
plt.grid(linestyle="--", alpha=0.5)
plt.show() 
```

[![](img/7a33c81f76e55c37e222b8544aef63dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g6Pm4t_c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ajv784n75th5vaxqqpv1.png)

这给你展示了电影分级数据。请注意，csv 文件中有很多记录，pandas 会立即执行。

相关链接:

*   [熊猫模块](https://pandas.pydata.org/)
*   [学习 Python](https://pythonprogramminglanguage.com/)
*   [熊猫文档](https://pandas.pydata.org/pandas-docs/stable/)