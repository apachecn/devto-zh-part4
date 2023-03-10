# 用熊猫读数据

> 原文：<https://dev.to/petercour/read-data-with-pandas-48dn>

在 [Python](https://pythonbasics.org) 中有一个模块可以帮助你解析数据。数据可以有多种形式(文件、表格、excel、sql、json)。由于历史原因，存在如此多的数据源。

处理数据的模块被命名为 [pandas 模块](https://pandas.pydata.org/)。

你可能知道你可以使用熊猫模块进行数据分析。但是你知道有很多方法可以读取数据吗？

```
pd.read_csv(filename)
pd.read_table(filename)
pd.read_excel(filename)
pd.read_sql(query, connection_object)
pd.read_json(json_string)
pd.read_html(url)
pd.read_clipboard() 
```

[![](img/7486c2a9312f5731698fc057688cf1d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzV-Z7pn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Fl4FGvUYI0tETAQwGk%252Fgiphy.gif%26f%3D1)

是的，您可以从许多来源读取数据。这些方法允许您快速获取数据

```
#!/usr/bin/python3 
import pandas as pd
data = pd.read_csv('yourfile.csv', header=None) 
```

如果您使用 MySQL 作为源代码

```
#!/usr/bin/python3
db = MySQLDatabase(DATABASE_HOST, DATABASE_USER, DATABASE_PASSWORD, DATABASE_NAME)
db_work_view = db.get_work_view()
connection = db_work_view._db_connection
df_people = pd.read_sql('select * from people', connection) 
```

读取 excel 文件

```
df = pd.read_excel('File.xlsx', sheetname='Sheet1') 
```

你明白了。Pandas 允许您快速从不同的数据源获取数据。它包括大多数现有的数据源。

相关链接:

*   [Python 数据分析库](https://pandas.pydata.org/)
*   [学习 Python 编程](https://pythonbasics.org)
*   [Python 练习](https://pythonbasics.org/exercises/)