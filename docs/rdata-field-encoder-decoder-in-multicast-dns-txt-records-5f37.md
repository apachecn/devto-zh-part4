# 📦多播 DNS TXT 记录中的 RDATA 字段编码器/解码器

> 原文：<https://dev.to/samandar/rdata-field-encoder-decoder-in-multicast-dns-txt-records-5f37>

```
from functorflow import ff

data = {'zero': 0, 'one': 1}
r = ff('dns-txt').encode(data)
print(r)
# {'type': 'Buffer', 'data': [6, 122,..]} 
r = ff('dns-txt').decode(r)
print(r) # {'zero': '0', 'one': '1'} 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/dns-txt?lite=true](https://repl.it/@functorflow/dns-txt?lite=true)