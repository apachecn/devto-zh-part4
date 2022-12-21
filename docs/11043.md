# 将整数转换为 Base64 编码的 VLQ 字符串

> 原文：<https://dev.to/samandar/convert-integers-to-a-base64-encoded-vlq-string-5fli>

更:[https://en.wikipedia.org/wiki/Variable-length_quantity](https://en.wikipedia.org/wiki/Variable-length_quantity)

```
from functorflow import ff

r = ff('vlq').encode(123)
print(r) # '2H' 
r = ff('vlq').encode([ 123, 456, 789 ])
print(r) # '2HwcqxB' 
r = ff('vlq').decode( '2HwcqxB' )
print(r) # [ 123, 456, 789 ] 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/vlq?lite=true](https://repl.it/@functorflow/vlq?lite=true)