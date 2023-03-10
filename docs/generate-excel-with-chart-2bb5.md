# 用图表生成 Excel

> 原文：<https://dev.to/petercour/generate-excel-with-chart-2bb5>

Excel 文件可以从代码中生成。在这种情况下，在 Python 中，包括一个图表。不懂 Python 的话，[先学 Python](https://pythonprogramminglanguage.com/) 。

普通 Python 是不够的。要开始，您需要安装 xlsxwriter。
这是一个写 excel 文件的 Python 模块。

然后定义 excel 文件名和工作表。

```
workbook = xlsxwriter.Workbook('new_excel.xlsx') 
worksheet = workbook.add_worksheet('sheet1') 
```

[![](img/f21a326b5f0f6f8d04d5ce737f61e4f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KRzwN5PR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Fize2r20ICQONq%252Fgiphy.gif%26f%3D1)

将数据写入 excel 表格。然后添加图表数据。这是不言自明的。

```
chart_col = workbook.add_chart({'type':'line'})      
chart_col.add_series(                                  
    {
        'name':'=sheet1!$B$1',
        'categories':'=sheet1!$A$2:$A$7',
        'values':   '=sheet1!$B$2:$B$7',
        'line': {'color': 'red'},
    }
)

chart_col.set_title({'name':'Beautiful Chart'})
chart_col.set_x_axis({'name':"X"})
chart_col.set_y_axis({'name':'Y'})       
chart_col.set_style(1) 
```

[![](img/25ff5773ca512019ce8f2d9ca0eab2b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_U7SYQbS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/21bntg8avm4qw01loeat.png)

使用折线图创建 excel 文件的完整代码:

```
#!/usr/bin/python3
import xlsxwriter

workbook = xlsxwriter.Workbook('new_excel.xlsx') 
worksheet = workbook.add_worksheet('sheet1')

headings = ['Number','testA','testB']         

data = [
     ['2017-9-1','2017-9-2','2017-9-3','2017-9-4','2017-9-5','2017-9-6'],
    [10,40,50,20,10,50],
    [30,60,70,50,40,30],
]                                                       

worksheet.write_row('A1',headings)

worksheet.write_column('A2',data[0])
worksheet.write_column('B2',data[1])
worksheet.write_column('C2',data[2])                  

chart_col = workbook.add_chart({'type':'line'})      
chart_col.add_series(                                  
    {
        'name':'=sheet1!$B$1',
        'categories':'=sheet1!$A$2:$A$7',
        'values':   '=sheet1!$B$2:$B$7',
        'line': {'color': 'red'},
    }
)

chart_col.set_title({'name':'Beautiful Chart'})
chart_col.set_x_axis({'name':"X"})
chart_col.set_y_axis({'name':'Y'})        

chart_col.set_style(1)

worksheet.insert_chart('A10',chart_col,{'x_offset':25,'y_offset':10}) 

workbook.close() 
```

相关链接:

*   [xlsxwriter 模块](https://xlsxwriter.readthedocs.io/)
*   [Python 教程](https://pythonprogramminglanguage.com/)
*   [用 Python 进行机器学习](https://pythonprogramminglanguage.com/python-machine-learning/)