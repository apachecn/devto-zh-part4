# 📦将 JSON 中的键转换为 camelCase 或 snake_case

> 原文：<https://dev.to/samandar/convert-keys-in-an-json-to-camelcase-or-snakecase-fa4>

```
from functorflow import ff

json = {...}

r = ff('json-case').camelize(json, recursive=True) 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/json-case?lite=true](https://repl.it/@functorflow/json-case?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。