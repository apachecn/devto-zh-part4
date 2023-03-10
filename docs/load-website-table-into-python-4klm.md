# 将网站表加载到 Python 中

> 原文：<https://dev.to/petercour/load-website-table-into-python-4klm>

在网上你可以找到无数的表格。那些表格(和任何网页)是用 HTML 定义的。所以你需要解析 HTML 对吗？

不完全是，有一个叫熊猫的模块可以为你解析数据。然后，这些数据被存储在一个名为数据帧的数据结构中。

假设你从[https://www.fdic.gov/bank/individual/failed/banklist.html](https://www.fdic.gov/bank/individual/failed/banklist.html)手中抢到桌子

```
#!/usr/bin/python3
import pandas as pd
import numpy as np

url ='https://www.fdic.gov/bank/individual/failed/banklist.html'
res2=pd.read_html(url)
print(res2)
print("+"*50)
print(res2[0]["Bank Name"]) 
```

所以这条线

```
res2=pd.read_html(url) 
```

获取整个表并将其放入 pandas 数据框中。这么简单。

[![](img/1d337a024a88806f47c01321bccb3695.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tqIkG9-G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Ftse3.mm.bing.net%252Fth%253Fid%253DOIP.8gmEsSh01otUQ9u9Ek4izAHaFi%2526pid%253DApi%26f%3D1)

这一行显示了整个表格

```
print(res2) 
```

对于特定的列

```
print(res2[0]["Bank Name"]) 
```

因此，您可以轻松地从网页中获取数据，而不必自己解析 html 语言。

*   [熊猫数据分析模块](https://pandas.pydata.org/)
*   [学习 Python 编程](https://pythonprogramminglanguage.com/)
*   [读取 CSV 数据(而不是 HTML)](https://pythonprogramminglanguage.com/read-csv/)
*   [官方熊猫文件](https://pandas.pydata.org/pandas-docs/stable/)