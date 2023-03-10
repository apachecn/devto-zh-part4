# 设置更高效的开发环境

> 原文：<https://dev.to/sendilkumarn/be-productive-embracing-shortcuts-crystalball-tada-4om7>

作为开发人员，我们喜欢那些古怪的工具和古怪的快捷方式。它们不仅让你看起来更聪明🤓而且让我们更快🚀和多产的✨.

这里有一个工具/技巧列表，可以让你的开发环境更加高效。

# 1\. Lazygit you beauty ❤️

今天 Git 无处不在。

*   如果你还在使用 SVN 或 PVCS，祝你好运。
*   如果你想知道它们是什么，不要为此烦恼。

lazygit 简直棒极了。它将终端转换成 UI。

[![](img/28fa8d9e8151514a9a5a1fe5d0fed27c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M69hg19u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8c2dhhboab0v36pj0hhz.png)

*   你所要做的就是运行`lazygit`或`lg`(我希望你知道如何创建别名😉).

*   然后检查文件，并使用`spacebar`卸载您想要的文件。

*   最后，用`c`提交它们。

*   输入您的提交消息，然后按`enter`。

太容易了。

如果你经常使用`GitHub`，那么你也应该检查一下[中枢](https://hub.github.com)。

有了中枢，你可以做更多有趣的事情:

您可以使用
克隆一个回购

```
hub clone org/repo 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用
浏览问题

```
hub browse --issues 
```

Enter fullscreen mode Exit fullscreen mode

> (不再需要打开浏览器和输入网址)

如果你像我一样，执行大量的代码审查。下载 PRs 将是一个巨大的麻烦。但是有了 hub，你可以检查 pr

```
hub pr checkout <pr_no> 
```

Enter fullscreen mode Exit fullscreen mode

如果请购单被更新，则用
提取变更

```
hub am -3 https://github.com/org/repo/pull/<pr_no> 
```

Enter fullscreen mode Exit fullscreen mode

他们也有`hub create`命令，可以在 Github 中用你的用户名创建项目。🎉

将你从简单的命令、点击和复制中解放出来。

> ![sendilkumarn profile image](img/fc35d4df971e615e670ce270243b7352.png)sendilkumarn[@ sendilkumarn](https://dev.to/sendilkumarn)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ Sarah _ edo](https://twitter.com/sarah_edo)hub+lazygit 让它更牛逼2019 年 6 月 23:06 日[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1139670456145973248)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1139670456145973248)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1139670456145973248)【T39】

# 2。代码代码代码💻

记事本是我的第一个编辑器(现在，不要评价我)。IDEs 从那时起发展了很多。但是 VSCode 是 ide 世界中一个重要的范式转变。

它是光滑的，现代的，可定制的，用 JavaScript 编写的(技术上是打字稿，但编译成 JavaScript 是正确的)。

但是对 VSCode 的定制是下一个级别。你可以调整你想要的每一个小细节。

液体错误:内部

我喜欢字体和简洁的设计。我经常摆弄字体，我认为字体和你的生产力之间有联系(研究眼镜👓开)。

我主要用

*   SF Mono(苹果字体)
*   菲拉码 San-[这里](https://github.com/tonsky/FiraCode)
*   Menlo(Mac 版 VSCode 的默认字体)
*   Ubuntu——主要是因为我在编程的早期对 Ubuntu 做了很多改动。

字体只是一个开始，对于一个精彩的体验，你应该有一个精彩的主题🎆。

谁会不喜欢精心制作的主题呢？

事实上，在我演讲之后，我收到了很多关于我所使用的主题的问题。人们总是喜欢好的(和简约的)设计。

最近的一个我很喜欢的是 Synthwave'84。这太疯狂了。

点击查看更多关于如何安装和使用 Synthwave'84 [的信息。我很确定你会喜欢它，因为它会发光💡。](https://marketplace.visualstudio.com/items?itemName=RobbOwen.synthwave-vscode)

[![](img/6f08e090c7ba5bd731df7df87176e668.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--arJLzX85--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zwagrwmom8ocnqzok4qo.png)

但是还有其他我喜欢的主题。他们是

*   [夜猫子](https://marketplace.visualstudio.com/items?itemName=sdras.night-owl)
*   钴 2
*   VSCode 灯光主题(默认)

用图标增加 VSCode 的趣味。点击查看插件

现在，我正在摆弄 WebAssembly 文本格式(看起来有点像 LISP)，括号对着色插件对我来说是强制性的。看看这个[这里](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer)。

它巧妙地给配套的支架上色，让它变得简单。

最后是捷径:

我最喜欢的一个快捷键是`cmd + a` + `cmd + k` + `cmd + 0`。这将折叠所有代码段。因此，您将对文件的功能有一个更清晰的了解，然后您可以深入了解具体的功能。

[![](img/71fd35018ddf080ced4ebeaec719fd9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BM4dOTG2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yrir06ok3ysdnxrmy9oo.png)

使用`cmd + ctrl + right | left`垂直分割编辑器视图。

通过`cmd + option + right | left`浏览文件

对于任何其他动作，键入`cmd + shift + p`打开动作菜单。你可以从这个菜单中选择任何东西。

有很多键盘快捷键。点击查看官方指南

# 3。巴什！巴什！！巴什！！！

谁不喜欢 bash🔳？

如果您使用的是 Mac，那么很有可能您使用的是 bash 的旧版本。是时候更新了，看看[这里](https://itnext.io/upgrading-bash-on-macos-7138bd1066ba)怎么样。

我爱`zsh`和它的主题(谁不爱)。

就我个人而言，我使用的是定制版的子弹头列车。

[![](img/15b150fcf4e3cfb86790dc6cb020dd49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2MFyUQS2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6kd9bopzzwvierj2u7wk.jpeg)

因为我接触过许多不同的编程语言及其版本。我经常忘记我运行的是什么版本。

我稍微调整了一下`bullet-train`,根据目录内容加入了我经常使用的编程语言。我知道它不完整也不庞大，但它给了我巨大的帮助。点击查看补丁版本[。](https://gist.github.com/sendilkumarn/919f52a3bcb1a856c2bf00465de92ccd)

# 4。旅行小贴士

性格内向，喜欢旅行🚌很多时候，我的笔记本电脑是我最亲爱的朋友🙏。通常是✈️航班或火车🚋ride 是我做演示和制作演示的地方。

如果你正在做，那么这将会引起你的兴趣。

*   首先，让您的 Mac 尽可能地睡眠。

`System Preferences > Energy Saver`

[![](img/813bd143f93764f09b2cbb0bbdc18104.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EJ9DMMbB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yi6umkknkz1cxozb59w0.png)

*   接下来，如果你正在使用 Docker For Mac | Windows，请关闭它们。

*   永远不要梦想打开松弛😆。慎用。

*   关闭 Wi-fi |蓝牙，如果你不需要它们的话。

*   降低显示器亮度。

这些小东西将帮助你延长电池寿命。

> 但是，嘿，别忘了在移动中保持水分。

# 5。通常

观察你的行动，如果你觉得有一件事你经常做，而且是平凡而重复的。寻找一种更快捷的方法来做这件事。如果你发现了一个很棒的方法，请与✨.社区分享

有时，你会看到人们在演示中做一些疯狂的键盘快捷键，或者你的同事在工作时也这样做。只要问他们，他们中的大多数人会非常乐意分享。

> 热情一点，开始黑吧！

* * *

如果你有一个很酷的主题|字体|快捷方式，请在这里评论它。它会帮助任何潜伏在这里的人。

关于生产力的更多信息:

[![sendilkumarn image](img/75ababb1cfcd601b558af3fa3f9c9e35.png)](/sendilkumarn) [## ✨Awesome 字体编码💻

### sendil Kumar N Jun 22 ' 203 分钟阅读

#productivity #coding #discuss #watercooler](/sendilkumarn/what-the-font-o71)[![sendilkumarn image](img/75ababb1cfcd601b558af3fa3f9c9e35.png)](/sendilkumarn) [## 提高浏览器开发工具技能的 9 种方法

### sendil Kumar N Apr 9 ' 202min read

#webdev #css #javascript #browsers](/sendilkumarn/level-up-your-browser-skills-8gh)[![sendilkumarn image](img/75ababb1cfcd601b558af3fa3f9c9e35.png)](/sendilkumarn) [## Git 基础——git+hub 让✨变得简单💡👍

### sendil Kumar N Jul 15 ' 192min read

#github #git #tutorial #beginners](/sendilkumarn/git-with-hub-makes-it-easy-some-git-101-51jo)

* * *

如果您有任何问题/建议/觉得我错过了什么，请随时添加评论。

你可以在 [Twitter](https://twitter.com/sendilkumarn) 关注我。

如果你喜欢这篇文章，请留下赞或评论。❤️