# While vs for

> 原文：<https://dev.to/elasticrash/while-vs-for-ig9>

这是一个有趣的小故事。几个月前，我写了下面的函数，因为需要得到一个字符在流(是的，stream)中特定出现的索引(独占模式，我的意思是你传递的位置不是搜索的一部分)。基本上，它用于对来自 EventSource 的每条消息进行一个小的验证。

在写函数的时候，我没有多想。我写了 3 个单元测试，然后写了函数，没想太多。

```
 private getNthIndex(text: string, delimiter: string, occurrence: number, position: number):number {
    while (occurrence-- && position++ < text.length) {
      position= text.indexOf(delimiter, position);
      if (position < 0) break;
    }
    return position;
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，一个团队成员有一张票来改变该区域周围的一些东西。当遇到那个函数时，他们告诉我，它完全令人困惑，需要一点思考才能正确理解它，如果我介意，它是否被重构。

自从我写那篇文章以来，已经过去了将近 9 个月，因为我确实花了几分钟才想起为什么要这样做，所以我同意，如果一个可读性更强的版本是可能的，显然我会欢迎它。

所以他们想出了下面的

```
 private getNthIndex(text: string, delimiter: string, occurrence: number, position: number):number {
    for (let i = 0; i < occurrence; i++) {
      position= text.indexOf(delimiter, position+ 1);
      if (position < 0 || position>= text.length) break;
    }
    return position;
  } 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，它们几乎是一样的，具有相同的可读性。只有两个小的变化:`while`循环现在是一个`for`循环，事实是计数器增加而不是减少。

所以，只是为了好玩，我把它贴在了`dev channel`上询问意见，奇怪的是，我意识到一些开发人员真的很怀疑 while 循环，而且`for`循环比`while`循环更容易阅读。

我以 1 比 6 输了🎉