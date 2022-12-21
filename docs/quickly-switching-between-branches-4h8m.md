# 分支间快速切换

> 原文：<https://dev.to/dzhavat/quickly-switching-between-branches-4h8m>

这篇文章最初发表在我的博客上。

* * *

似乎我昨天关于[和`git checkout`T2 一起变得富有成效的帖子引起了很多人的共鸣。有些人甚至写信告诉我，他们发现这个提示很有用。希望他们也能在工作中使用它。](https://dev.to/dzhavat/getting-productive-with-git-checkout-2299)

[![dzhavat](img/f3a50c17296423d037e4a3e2cb68a7bf.png)](/dzhavat) [## 使用 git checkout 提高工作效率

### dzhavat Ushev 7 月 22 日 192 分钟阅读

#git #productivity](/dzhavat/getting-productive-with-git-checkout-2299)

现在，我正坐在回家的火车上，有一点时间来听另一个小贴士。这是一件非常简单和容易做到的事情，但同时每次我使用它都会给我带来快乐。我就是这样在树枝间切换的。

说我刚从`master`换到`new-feature`。我在那里做过一些工作，想回去。他们通常这样做的方式是:

```
git checkout master 
```

Enter fullscreen mode Exit fullscreen mode

结果是，如果我使用`-`(破折号)代替分支名称，`git`将切换到我最后访问的分支。于是上面就变成了:

```
git checkout - 
```

Enter fullscreen mode Exit fullscreen mode

再次运行相同的命令会将我带回`new-feature`。显然，使用这个命令只会在这两个分支之间切换。如果我想去第三个，我还需要写它的名字。