# 当你用 Stackoverflow 排序时，接受的答案是错误的

> 原文：<https://dev.to/jay_wick/when-you-so-done-with-stackoverflow-sorting-accepted-answers-wrong-47lj>

**拿着我的啤酒，我写一个网络扩展**

我知道有些已经存在，但没有尊重他们的超级奇怪的锚定系统。

目前为止的临时代码是

```
[...document.querySelectorAll('#answers > div.answer')].reduce((acc, el) => [...acc, {anchor: el.previousElementSibling, answer: el, votes: +el.querySelector('div[itemprop="upvoteCount"]').getAttribute('data-value')}], []).sort((a, b) => b.votes - a.votes) 
```

Enter fullscreen mode Exit fullscreen mode