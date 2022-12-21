# difflib -从列表中查找字符串的近似匹配

> 原文：<https://dev.to/wangonya/difflib-finding-close-matches-of-strings-from-a-list-54jl>

假设我们有一个字符串列表:`_list = [...,]`和用户输入`_input = '...'`，我们如何在`_list`中找到与`_input`最相似的条目？

Python 内置了一个名为 [`difflib`](https://docs.python.org/2/library/difflib.html) 的包，里面的函数`get_close_matches()`可以帮助我们。

`get_close_matches(word, possibilities, n, cutoff)`接受四个参数:

*   `word` -在我们的列表中查找最匹配的单词
*   `possibilities` -在其中搜索`word`的近似匹配的列表
*   `n`(可选)-要返回的最大相近匹配数。必须是`> 0`。默认是`3`。
*   `cutoff`(可选)-一个在[0，1]范围内的浮点值，一个`possibility`必须得到这个值才能被认为与`word`相似。`0`很宽大，`1`很严格。默认为`0.6`。

一个来自[文档](https://docs.python.org/2/library/difflib.html#difflib.get_close_matches) :
的例子

```
Python 3.7.3

>>> from difflib import get_close_matches
>>> get_close_matches('appel', ['ape', 'apple', 'peach', 'puppy'])
['apple', 'ape'] 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子可以很容易地修改为使用定制列表`_list`作为`possibilities`，使用用户输入`_input`作为`word`。