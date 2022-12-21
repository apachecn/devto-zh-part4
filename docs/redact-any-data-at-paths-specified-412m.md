# 📦编辑指定路径上的任何数据

> 原文：<https://dev.to/samandar/redact-any-data-at-paths-specified-412m>

```
from functorflow import f

# Redact any data at paths specified. 
data = {
  'x': {'c': {'d': 'hide me', 'e': 'leave me be'}},
  'y': {'c': {'d': 'and me', 'f': 'dont touch'}},
}

r = f('redact', data, paths = ['*.c.d'] )
print(r) # {"x":{"c":{"d":"[REDACTED]","e":"leave me be"}},"y":{"c":{"d":"[REDACTED]","f":"dont touch"}}} 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/redact?lite=true](https://repl.it/@functorflow/redact?lite=true)