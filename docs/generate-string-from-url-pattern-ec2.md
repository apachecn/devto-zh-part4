# 📦从 URL 模式生成字符串

> 原文：<https://dev.to/samandar/generate-string-from-url-pattern-ec2>

```
from functorflow import ff

# Generate string from URL pattern and values: 

pattern = ff("make-url-pattern", pattern="/api/products(/:id)")
res = pattern.stringify( vals={ "id": 43 } ) 

print(res) # '/api/products/43' 

# Match pattern against string and extract values: 
pattern = ff("make-url-pattern", pattern="/api/products(/:id)")
res = pattern.match( url="/api/products/5" ) 

print(res) # {'id': '5'} 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/make-url-pattern?lite=true](https://repl.it/@functorflow/make-url-pattern?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。