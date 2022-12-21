# Git 提示——为什么你不应该保留一个本地的主支行？

> 原文：<https://dev.to/slashgear_/git-tip-why-you-should-not-keep-a-local-master-branch-3400>

当使用`git`时，我经常犯这样的错误:使用这些命令维护本地更新的`master`分支。

```
git checkout master

git pull origin master 
```

Enter fullscreen mode Exit fullscreen mode

我已经做过很多次了。这允许我通过了解最新的变化来开始开发。

事实上，没有必要维护我们不直接工作的分支的本地副本。我们已经可以访问远程分支的完整本地版本。

这段时间我本可以做的就是:

```
git checkout origin/master 
```

Enter fullscreen mode Exit fullscreen mode

当你想做新东西时，只需使用

```
git checkout -b my-feature origin/master 
```

Enter fullscreen mode Exit fullscreen mode

请记住，这对于*主*以及您不直接工作/您将*提交*的所有其他分支都有效。

你也可以听从我同事的建议。

> ![Florent Lepretre profile image](img/1d75b82498ba5bf1a49a826bfaf7ae7a.png)😉就我个人而言，我有一个 git rem 别名“git rebase origin/master”，很容易写👌2019 年 7 月 24 日下午 17:21[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1154079231195959298)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1154079231195959298)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1154079231195959298)

> ![Florent Lepretre profile image](img/1d75b82498ba5bf1a49a826bfaf7ae7a.png)Florent Lepretre[@ superlaw](https://dev.to/superflaw)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Slashgear _](https://twitter.com/Slashgear_)一般来说，您应该保留最少的本地分支，当您推送它们时，您在本地存储库中就有了它们的远程映像。除非你修剪它们，否则它们不会消失。只要习惯阅读什么' git fetch '打印，一切都在那里！2019 年 7 月 24 日下午 17:27[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1154080561423691776)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1154080561423691776)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1154080561423691776)

如果你对 Git 有其他的技巧，请告诉我！