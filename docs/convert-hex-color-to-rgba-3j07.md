# 📦将十六进制颜色转换为 RGBA

> 原文：<https://dev.to/samandar/convert-hex-color-to-rgba-3j07>

```
from functorflow import f

r = f('hex-rgba', '#4183c4')
print(r) # {'red': 65, 'green': 131, 'blue': 196, 'alpha': 1} 

r = f('hex-rgba', 'cd2222cc')
print(r) # {'red': 205, 'green': 34, 'blue': 34, 'alpha': 0.8} 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/hex-rgba?lite=true](https://repl.it/@functorflow/hex-rgba?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。