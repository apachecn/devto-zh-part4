# 📦HTML 消毒剂

> 原文：<https://dev.to/samandar/html-sanitizer-814>

```
from functorflow import f

dirty = '<script>alert("xss");</script><p>hello'
clean_html = f('sanitize-html', dirty)
print(clean_html) # '<p>hello</p>' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/sanitize-html?lite=true](https://repl.it/@functorflow/sanitize-html?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。 [FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。