# 从代码编写 excel

> 原文：<https://dev.to/petercour/write-excel-from-code-37bh>

可以用 Python 写 Excel 文件。在开发社区之外，Excel 是一种非常流行的文件格式。金融等很多领域大量使用 Excel。

[![](img/6314fb09c4326aec4ba390a6c0b0280a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_r4pnvd1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.tenor.com%252Fimages%252Feb36c010b6c42265389749355ae2e620%252Ftenor.gif%26f%3D1)

xlsxwriter 模块允许您这样做，首先安装该模块。
然后用 Python 加载模块。

```
#!/usr/bin/python3
#coding: utf-8
import xlsxwriter 
```

创建要使用的文件和图纸

```
file_name = "data.xlsx"
workbook = xlsxwriter.Workbook(file_name)
worksheet = workbook.add_worksheet('sheet1') 
```

将标题写入 excel 表格

```
worksheet.write(0, 0, 'id')
worksheet.write(0,1, 'name')
worksheet.write(0,2, 'class')
worksheet.write(0,3, 'data') 
```

最后写入数据

```
worksheet.write_row(1, 0, [1, 2, 3])
worksheet.write_column('D2', ['a', 'b', 'c'])

workbook.close() 
```

[![](img/66c44bd22c88fdaa1f6bf99e83fa20ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dXgWR6xO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Ftse2.mm.bing.net%252Fth%253Fid%253DOIP.vsJ2gM02_8oW-TGRzNOkYAHaEg%2526pid%253DApi%26f%3D1)

将所有这些放在一起编写 excel 文件。它给你这个代码:

```
#!/usr/bin/python3
#coding: utf-8
import xlsxwriter

file_name = "data.xlsx"
workbook = xlsxwriter.Workbook(file_name)
worksheet = workbook.add_worksheet('sheet1')

worksheet.write(0, 0, 'id')
worksheet.write(0,1, 'name')
worksheet.write(0,2, 'class')
worksheet.write(0,3, 'data')

worksheet.write_row(1, 0, [1, 2, 3])
worksheet.write_column('D2', ['a', 'b', 'c'])

workbook.close() 
```

相关链接:

*   [熊猫模块](https://pandas.pydata.org/)
*   [XLSX 写手](https://xlsxwriter.readthedocs.io/)
*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)