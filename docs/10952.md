# 📦将 JSON 数组转换为 CSV，反之亦然

> 原文：<https://dev.to/samandar/convert-json-array-to-csv-and-vice-versa-370l>

```
from functorflow import ff
from countries import json_countries

# Convert JSON array to CSV csv = ff("json-csv").csv(json_countries)

print(csv)

# Convert CSV back to JSON array 
json_array = ff("json-csv").json(csv)

print(json_array) 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/json-csv?lite=true](https://repl.it/@functorflow/json-csv?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。