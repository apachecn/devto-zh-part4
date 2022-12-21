# 检查字符串是否有 ANSI 转义码

> 原文：<https://dev.to/samandar/check-if-a-string-has-ansi-escape-codes-4bgo>

```
from functorflow import f

r = f('ansi-esc', '\u001B[4mUnicorn\u001B[0m')
print(r) # True 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/ansi-esc?lite=true](https://repl.it/@functorflow/ansi-esc?lite=true)