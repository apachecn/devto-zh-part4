# 在 git 中使用- force 的危险

> 原文：<https://dev.to/corydhmiller/the-danger-of-using-force-in-git-2h2i>

昨天，我的生活似乎在我眼前一闪而过。我盯着分支机构的日志，想知道为什么前一天的工作都不见了。

我开始恐慌，跑着`git log`和`git reflog`，但是我怎么也想不出为什么什么都没有保存下来。我*确信*我已经保存并提交了我的工作，那么为什么现在我会看到我的代码处于两天前的状态呢？

在意识到这一点之前，我已经犯了几个错误，这些错误最终导致了大量重要工作的丢失。

这里有一些关于如何避免像我这样的简单错误的建议。

### 累了就不要做复杂的工作。

老实说，我真的不知道我昨天大部分时间在想什么。我前一天晚上睡得很不好，以为我能挺过去。

有时，全力以赴也许是可能的，但是当你的大脑疲劳时，你更有可能犯错误，而错误是要付出代价的。

花些时间让你的头脑清醒一整天。经常休息一下，离开电脑，这样你就可以带着清晰的头脑回到你的代码中。如果可能的话，小睡一两次。

最重要的是，保护你的睡眠和休息时间，因为这不仅仅是晚上睡几个小时。疲劳有能力真正毁掉你的工作。

### 制定一个流程，坚持下去。

我承认我对我的命令很草率。我最近买了一台新的笔记本电脑，所以我一直在为它安装应用程序、1 密码和 SSH 密钥以及其他所有东西。其中包括与我们在 GitHub 上的回购同步。

我仍然不知道我使用了哪一组命令来同步当天的工作和在线回购，但这实际上是问题的关键。事实上，我不记得我是否`git pull`或`git push`检查过哪个分支有最新版本的代码，这才是我真正的错误所在。

因为我没有写下一个过程，我只是让它发生。我想我知道我在做什么，所以为什么要麻烦复杂的检查呢？

我们费心检查自己，因为我们是人，我们会犯错误。

即使我有像“当你`git pull`有差异时该怎么办”或“当你的本地回购落后于或领先于原始回购时”这样简单的流程，它也能让我省去一些真正的麻烦。

在任何情况下，我拍打周围的一些命令，并认为这将是没问题的。不是的。

拨入进程并记下它们有助于防止问题的发生(和重复发生)。

### 除非实在没有其他选择，否则不要使用`--force`。

...然后仍然寻找另一种选择。

然后是这个故事的这一部分，我真的感到非常尴尬。在某一点上，我 100%确信我的分支的本地副本是最新的，所以当出现那些`your branch is 5 commits ahead and 6 behind`消息之一时，我想我可以通过将我的本地历史强制到远程回购来绕过这个问题。

于是我跑了`git push --force`。

瞧，它完全按照我告诉它的那样做了，并且覆盖了远程分支上的历史记录。

这是一天的早些时候，我没有想太多，因为我在做其他的工作。直到我坐下来处理我的分支，发现我所有的工作都不见了。

公平地说，我不知道*为什么*我的本地分支没有最新的代码。我已经完全保存并提交了工作，所以我仍然很困惑，但无论是哪种情况，我所做的事情都有很大的不同。

**当`git`给你警告时，很可能是你的错。**

我想走捷径，而不是花额外的几分钟去检查分支和日志，看看哪里有差异，如果有什么问题，就做一个快速的分支拷贝以防万一。

开发过程中的捷径有助于节省时间，但也会导致严重的错误。坚持这个过程，把它做好，给自己省点头疼的事。

## 然而不知何故，我拿回了我的代码。

这种疯狂有一个快乐的结局。在部署之前，我们通过一个持续集成和测试套件运行我们的代码，所以在我在大约 20 分钟的时间内死了 1000 次之后，我想检查一下我是否真的在前一天提交并推送了我的更新。

谢天谢地，我做到了！GitHub 有一个我不知道的好东西，它保存每一次提交的提交，即使历史已经被覆盖。至少，看起来是这样的，有人可以在这一点上验证我。不管怎样，即使提交不在分支历史中，我仍然能够访问它并取回我的代码，为我节省了一两天的工作。

我不想指望这样的奇迹，所以我从现在开始坚持这个过程，努力犯新的错误，而不是重复旧的错误。

我还需要在我的电脑上自动运行某种宏，每次我输入时都会删除`--force`。