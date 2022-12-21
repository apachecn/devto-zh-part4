# 将字符串数据(矩阵)转换为 CSV 文本

> 原文：<https://dev.to/samandar/stringify-data-matrix-into-a-csv-text-3k6h>

```
from functorflow import f

arr = [['1','2','3','4'],['a','b','c','d']]

r = f('stringify-csv', arr)
print(r) 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/stringify-csv?lite=true](https://repl.it/@functorflow/stringify-csv?lite=true)