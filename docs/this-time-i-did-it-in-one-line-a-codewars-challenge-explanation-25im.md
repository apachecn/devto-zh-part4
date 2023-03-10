# 这一次我用一行代码完成了它——CodeWars 挑战解释

> 原文：<https://dev.to/ekand/this-time-i-did-it-in-one-line-a-codewars-challenge-explanation-25im>

我已经养成了写[帖子](https://dev.to/erikkristoferanderson/how-did-they-do-that-in-one-line-part-2-isograms-52p6)的习惯，这些帖子是关于 CodeWars 上形(挑战)的一行解决方案。

过去它们都是关于其他代码战士写的一行解决方案。但是这一次，我想到了一句俏皮话。我不是第一个，但确实是我自己想出来的。

这个[形](https://www.codewars.com/kata/exes-and-ohs/python)的想法是确定一个字符串是否有相等数量的“x”和“o”。

这里是我的解决方案:

```
def xo(s):
    return s.lower().count('x') == s.lower().count('o') 
```

Enter fullscreen mode Exit fullscreen mode

注意:我说的一行是指函数体的一行。

我从 return 语句开始，因为它后面的表达式将返回一个布尔值:`True`如果“x”和“o”的数目相同，否则返回`False`。

现在有点密了，我就把它拆开。在等式测试的每一边，我们都从`s.lower()`开始。该表达式的结果将是小写字符串。姑且称之为`s.low`。

现在我们可以看到`s.lower().count('x')`等价于 s_low.count('x ')，它将返回字符串`s`中字符“x”的编号。

`s.lower().count('o')`也会做同样的事情。

现在，在`==`的两边，我们有一个表示字符串`s`中某个字符的数字的表达式。如果这些数字相等，函数返回`True`。否则，函数返回`False`。

注意:更具可读性的解决方案应该是:

```
def xo(s):
    s = s.lower()
    return s.count('x') == s.count('o') 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是我的第一个想法，也是 CodeWars 上最流行的解决方案。因为 Python 是关于可读性的，我不得不说这是更好的解决方案，但是我喜欢单行解决方案。

我想在不久的将来，我会带着另一个这样的东西回来。在那之前，好好享受吧。

~埃里克