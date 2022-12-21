# 📦解析 HTML 并返回漂亮的文本

> 原文：<https://dev.to/samandar/parses-html-and-returns-beautiful-text-51p1>

```
from functorflow import f

html = '<h1>hello world</h1>'
r = f('html-to-text', html, options={'wordwrap': 130})
print(r) # HELLO WORLD 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/html-to-text?lite=true](https://repl.it/@functorflow/html-to-text?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。