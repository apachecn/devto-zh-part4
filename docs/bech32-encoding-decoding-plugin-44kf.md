# Bech32 编码/解码插件

> 原文：<https://dev.to/samandar/bech32-encoding-decoding-plugin-44kf>

符合 BIP173 的 Bech32 编码/解码插件。

```
from functorflow import ff

s = 'abcdef1qpzry9x8gf2tvdw0s3jn54khce6mua7lmqqqxw'
r = ff('bech32').decode(s)

print(r) # { 'prefix': 'abcdef', 'words': [0,1,2,3,4,5,6,7,8,9,...] } 

r = ff('bech32').encode(prefix='foo', words='foobar')

print(r) # 'foo1vehk7cnpwgry9h96' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/bech32?lite=true](https://repl.it/@functorflow/bech32?lite=true)