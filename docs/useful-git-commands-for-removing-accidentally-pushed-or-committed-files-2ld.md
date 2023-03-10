# 用于删除意外推送或提交的文件的有用 Git 命令

> 原文：<https://dev.to/morinoko/useful-git-commands-for-removing-accidentally-pushed-or-committed-files-2ld>

我发现自己经常需要重置提交，因为我提交了一个我不想要的文件，或者忘记在提交中添加一些东西。每隔一段时间，我还需要从远程存储库中删除文件(但不是本地的)，因为我不小心推了它。我总是忘记命令，所以在这里给大家参考:)

## 删除已经推送到 git 存储库的文件

您可以使用下面的命令从 git repo 中删除一个文件，例如，如果您不小心把它推了上来，或者您不想让它再存在了。

```
git rm --cached name_of_file 
```

Enter fullscreen mode Exit fullscreen mode

这不会在本地删除它，所以如果你想把它保存在你的计算机上作为参考而不在 Git 上共享，它是安全的。为了防止它再次被推送到 Git，只需将文件添加到您的`.gitignore`中。

当然，如果你不再需要这个文件，也可以把它从你的系统中删除。

**但是！**如果文件包含敏感数据，比如密码或密钥，那么您需要做更多的工作，因为提交的内容仍然可以在存储库的历史中找到。

## 如果推送的文件有密码等敏感数据

正如下面[ol Baum 的评论](https://dev.to/oscherler/comment/d9ao)所指出的(谢谢！)，上面的方法不会*完全*从提交历史中删除文件。

为了从你的历史中删除敏感文件，你可以使用一个叫做 [BFG 回购清理器](https://rtyley.github.io/bfg-repo-cleaner/)的开源工具，或者使用 git 的`git filter-branch`。

与`filter-branch`相比，BFG 回购清洁器显然能更容易、更快速地完成工作。它允许您删除文件，或者用文本`***REMOVED***`替换文件中的密码或密钥。您也可以删除意外推送的大文件。

我自己没有使用过 BFG，但是查看一下 [BFG 回购清洗器的文档和下载页面](https://rtyley.github.io/bfg-repo-cleaner/)看看如何使用它。说明非常简单明了。

要了解`git filter-branch`，查看 git 帮助网站上的[使用过滤分支](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository#using-filter-branch)文章和 [git 过滤分支文档](https://git-scm.com/docs/git-filter-branch)。

## 删除您在上次提交时意外提交的文件(但尚未推送)

我习惯用`git add .`提交所有东西，所以有时我会不小心在一个单独的提交中添加了我实际上想要的文件。

要从尚未推送的提交中删除某些文件，首先，用
撤销上一次提交

```
git reset --soft HEAD^1 
```

Enter fullscreen mode Exit fullscreen mode

接下来，运行:

```
git rm --cached <file-name> 
```

Enter fullscreen mode Exit fullscreen mode

移除不想提交的文件。这会将它从提交中移除，并将其设置回未跟踪的文件。您应该能够通过快速 git 状态来确认。

现在，您可以像往常一样通过运行:
来提交其他文件

```
git commit -m "my commit message" 
```

Enter fullscreen mode Exit fullscreen mode

然后可以将删除的文件添加到单独的提交中！