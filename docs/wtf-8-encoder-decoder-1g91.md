# WTF-8 编码器/解码器。

> 原文：<https://dev.to/samandar/wtf-8-encoder-decoder-1g91>

白皮书:[https://simonsapin.github.io/wtf-8/](https://simonsapin.github.io/wtf-8/)

```
from functorflow import ff

r = ff('wtf-8').encode('\uD800\uDC01')
print(r) # '\xF0\x90\x80\x81' 
r = ff('wtf-8').decode('\xF0\x90\x80\x81')
print(r) # '\uD800\uDC01' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/wtf-8?lite=true](https://repl.it/@functorflow/wtf-8?lite=true)

停止浪费时间安装和配置 Python 库。马上使用它们。 [FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。