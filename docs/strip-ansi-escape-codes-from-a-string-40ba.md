# 📦从字符串中去除 ANSI 转义码

> 原文：<https://dev.to/samandar/strip-ansi-escape-codes-from-a-string-40ba>

```
from functorflow import f

r = f('strip-ansi', '\u001B[4mUnicorn\u001B[0m')
print(r) # 'Unicorn' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/strip-ansi?lite=true](https://repl.it/@functorflow/strip-ansi?lite=true)