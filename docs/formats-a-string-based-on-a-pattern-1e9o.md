# 📦基于模式格式化字符串。

> 原文：<https://dev.to/samandar/formats-a-string-based-on-a-pattern-1e9o>

```
from functorflow import f

print(f('format-str', 'YYYY-MM-DD', '20190508'))
# '2019-05-08' 

print(f('format-str', '2019-05-08', '20151217'))
# '2015-12-17' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/format-str?lite=true](https://repl.it/@functorflow/format-str?lite=true)