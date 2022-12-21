# 📦将数字转换成单词、序数词/数字

> 原文：<https://dev.to/samandar/converting-numbers-into-words-ordinal-words-numbers-526i>

```
from functorflow import ff

r = ff('number-words').words(-3)
print(r) # 'minus three' 

r = ff('number-words').ordinal(21)
print(r) # '21st' 

r = ff('number-words').wordsordinal(21)
print(r) # 'twenty-first' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/number-words?lite=true](https://repl.it/@functorflow/number-words?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。