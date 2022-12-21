# 📦十进制↔十六进制转换器

> 原文：<https://dev.to/samandar/decimal-hexadecimal-converter-4958>

```
from functorflow import ff

a = ff('hex-dec').dec(hex='0xfa')
# 250 
b = ff('hex-dec').hex(dec='250')
# '0xfa' 
c = ff('hex-dec').hex(dec='250', options={'prefix': False})
# 'fa' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/hex-dec?lite=true](https://repl.it/@functorflow/hex-dec?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。