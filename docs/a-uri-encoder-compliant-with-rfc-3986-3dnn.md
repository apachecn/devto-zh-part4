# 一种符合 RFC 3986 的 URI 编码器

> 原文：<https://dev.to/samandar/a-uri-encoder-compliant-with-rfc-3986-3dnn>

关于:[https://tools.ietf.org/html/rfc3986](https://tools.ietf.org/html/rfc3986)

```
from functorflow import f

# A URI encoder compliant with RFC 3986 
r = f('uri-encode', 'you!and*me')
print(r) # you%21and%2Ame 
```

Enter fullscreen mode Exit fullscreen mode

> 停止浪费时间安装和配置 Python 库。马上使用它们。 [FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。

[https://repl.it/@functorflow/uri-encode?lite=true](https://repl.it/@functorflow/uri-encode?lite=true)