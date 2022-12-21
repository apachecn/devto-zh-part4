# Python 3.8 中有什么新内容？

> 原文：<https://dev.to/petercour/what-s-coming-in-python-3-8-3djh>

Python 3.8 会增加什么？相当多的变化。不了解 Python，很好的时机[开始](https://pythonprogramminglanguage.com/)

*   海象运营商

将添加 walrus 运算符(:=)。那是因为只要有一点想象力，它看起来就像一头海象。

```
m = re.match(p1, line)
if m:
    return m.group(1)
else:
    m = re.match(p2, line)
    if m:
        return m.group(2)
    else:
        m = re.match(p3, line)
        ... 
```

变成了:

```
if m := re.match(p1, line):
    return m.group(1)
elif m := re.match(p2, line):
    return m.group(2)
elif m := re.match(p3, line): 
```

[![](img/2d0d5d766d043486d0f7ebcd76dbf783.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FS0yqisI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FnEgEKC8OVA4YU%252Fgiphy.gif%26f%3D1)

*   f 字符串的调试支持

你说的 f 弦是什么？你可以很容易地输出东西:

```
answer = 42
f'The answer is not {answer+1}'
f'The root of the answer is {math.sqrt(answer)}' 
```

比加+ str(答案)之类的东西简单多了。

*   仅位置参数
*   可移动的 **pycache**

以及更多的东西。您的旧 Python 3.x 代码仍然可以工作。

相关链接:

*   [Python 3.8 的新功能](https://lwn.net/SubscriberLink/793818/0c6f9dd271021cd4/)
*   [学习 Python 编程](https://pythonprogramminglanguage.com/)