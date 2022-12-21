# 📦编码 HTML 字符引用和字符实体

> 原文：<https://dev.to/samandar/encode-html-character-references-and-character-entities-348i>

```
from functorflow import f

val = 'alpha © bravo ≠ charlie 𝌆 delta'
r = f('encode-html', val)
print(r) # 'alpha &copy; bravo &ne; charlie &#x1D306; delta' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/encode-html?lite=true](https://repl.it/@functorflow/encode-html?lite=true)