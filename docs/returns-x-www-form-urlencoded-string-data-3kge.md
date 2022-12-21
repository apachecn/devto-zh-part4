# 📦返回“x-www-form-urlencoded”字符串数据

> 原文：<https://dev.to/samandar/returns-x-www-form-urlencoded-string-data-3kge>

```
from functorflow import f

data = {
  'str' : 'val',
  'num' : 0,
  'arr' : [3, {'prop' : False}, 1, None, 6],
}

r = f('form-urlencoded', data)
print(r)# str=val&num=0&arr%5B0%5D=3&arr%5B1%5D%5Bprop%5D=false&arr%5B2%5D=1&arr%5B3%5D=null&arr%5B4%5D=6 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/form-urlencoded?lite=true](https://repl.it/@functorflow/form-urlencoded?lite=true)