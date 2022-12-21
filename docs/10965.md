# 📦JSON 数组到 Markdown 转换器

> 原文：<https://dev.to/samandar/json-array-to-markdown-converter-2do4>

```
from functorflow import f

json_arr = [
    { 'h1': 'JSON To Markdown' }
  , { 'blockquote': 'A JSON to Markdown converter.' }
  , { 'h2': 'Features' }
]

result = f('json-md', json_arr)

print(result) 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/json-md?lite=true](https://repl.it/@functorflow/json-md?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。