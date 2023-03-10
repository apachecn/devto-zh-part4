# 亲爱的前端开发者，为什么交互使用 git 更好

> 原文：<https://dev.to/alexanderalemayhu/dear-frontend-devs-why-using-git-interactively-is-better-5d5g>

我在工作中从来没有运行过这两个命令

```
git add .
git commit -m 'my amazing changes' 
```

Enter fullscreen mode Exit fullscreen mode

为什么？因为你真的看不到自己在犯什么事。每天和每次我在办公室工作时，我都确保以这种方式提交更改(输出微调)

```
git add -p
[...]
git commit -v
[...] 
```

Enter fullscreen mode Exit fullscreen mode

使用`-p`选项运行`git add`会触发一种交互模式，在这种模式下，您可以遍历所有的大块(变更),并对它们进行登台、分割或忽略。对于回顾自己的改变真的很强大。当使用`-v`选项运行`git commit`时，您可以看到将要提交到历史中的更改。这是一个很好的地方进行最后的本地审查，在它被推出之前，你可以很容易地看到所有的位。这种方法不能捕获所有的错误，但它肯定能捕获许多愚蠢的错误。

如果你不喜欢简洁的命令，你也可以运行`git add --interactive`，这会给你一个更冗长的文本界面。

```
$ git add --interactive
           staged     unstaged path
  1:    unchanged        +2/-1 index.js
  2:    unchanged        +2/-2 package.json

*** Commands ***
  1: status   2: update   3: revert   4: add untracked
  5: patch    6: diff     7: quit     8: help
What now> 
```

Enter fullscreen mode Exit fullscreen mode

我鼓励你不要为了新的代码变更而漫不经心地运行`git add .`,而是使用交互模式有选择地添加你想要的变更，然后`git commit -v`提交并查看将要添加到历史中的变更。我不能做出任何保证，但我认为过一段时间后你会喜欢上这种方法的。

这确实是我的个人观点，不一定反映你的经历。我仍然希望你能有点开放的思想，尝试这种方法，看看它是否对你有用。如果你对 git 不太满意，我建议你试着写一篇本地日记，记录你所做的所有修改，比如修改错别字、修改行、创建文件等等。我发现这对我学习 git 帮助很大。

最后，如果你喜欢阅读，那么你必须去看看《Pro Git》这本书，它实际上是一个很好的免费资源。