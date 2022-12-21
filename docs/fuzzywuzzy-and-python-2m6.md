# Fuzzywuzzy 和 Python

> 原文：<https://dev.to/petercour/fuzzywuzzy-and-python-2m6>

什么是 fuzzywuzzy？

[![](img/205f7f05afa3b2d08b93b5e75bba843e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K56IozGL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F3XXcyZlvqo9tm%252Fgiphy.gif%26f%3D1)

这是一个字符串匹配模块。字符串是可以存储(和修改)文本的变量。它使用 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance)来计算简单易用的软件包中序列之间的差异。

像老板一样的模糊字符串匹配。
如何入门？首先你应该知道 [Python 编程](https://pythonprogramminglanguage.com/)。

```
pip install fuzzywuzzy 
```

那么你可以这样使用它:

```
#!/usr/bin/python3
from fuzzywuzzy import fuzz
from fuzzywuzzy import process

r = fuzz.ratio("this is a test", "this is a test!")
print(r)

r = fuzz.ratio("fuzzy wuzzy was a bear", "wuzzy fuzzy was a bear")
print(r) 
```

这将输出比率:

```
97
91 
```

您可以从解释器中运行:

```
>>> fuzz.ratio("this is a test", "this is a test!")
    97 
```

另一个模糊不清的例子:

```
>>> from fuzzywuzzy import fuzz
>>> fuzz.ratio("this is a test","a test this is")
50 
```

相关链接:

*   [Fuzzywuzzy 模块](https://github.com/seatgeek/fuzzywuzzy)
*   [学习 Python](https://pythonprogramminglanguage.com/)