# Git 提取带有历史记录的单个文件

> 原文：<https://dev.to/k4ml/git-extract-single-file-with-history-2hkj>

我们有一个 python 模块，当我们开始在多个项目中使用它时，我们希望将它拆分成自己的 repo。

我搜索“git extract file with history”的第一个结果是来自 [Stackoverflow 问题](https://stackoverflow.com/questions/7375528/how-to-extract-one-file-with-commit-history-from-a-git-repo-with-index-filter)。但是当我尝试的时候它并不真的工作。所有的分支和标记仍然保留。由于这种方法是“过滤掉所有你不想要的东西，保留你想要的东西”，所以对于历史非常悠久的大型回购来说，这是相当慢的。

有人还创造了一个叫做`[git-splits][2]`的扩展，灵感来自另一个[所以问题](https://stackoverflow.com/questions/5998987/splitting-a-set-of-files-within-a-git-repo-into-their-own-repository-preserving)。但是扩展看起来更像是提取目录而不是单个文件。

附注:我喜欢 git 扩展的一点是，你只需创建一个名为`git-something`的可执行文件，并将其添加到你的`$PATH`中，然后它就可以在`git something`中使用了。这是超级简单扩展 API 的一个例子。我创建了一个名为`git chlog`的命令，它将显示给定特定标签的漂亮的 changelog。

好了，回到提取单个文件。最后我找到了这篇[博文](https://www.pixelite.co.nz/article/extracting-file-folder-from-git-repository-with-full-git-history/)。它使用`git am`方法，通过从与文件相关的提交中生成补丁，并重放它以将文件从其初始状态重建到当前状态。

```
git log --pretty=email --patch-with-stat --reverse --full-index --binary -- src/project/pgq.py > /tmp/patch 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以应用这个补丁从原始回购协议中获得一个新的 pgq.py 及其历史:-

```
git init
git touch setup.py
git add setup.py
git commit -m'Initial'
git am < /tmp/patch 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这是不太好的部分。我必须先提交新文件，因为在应用补丁时，它中途失败，并出现错误:-

```
/home/kamal/git/pgq/.git/rebase-apply/patch:38: trailing whitespace.

/home/kamal/git/pgq/.git/rebase-apply/patch:42: trailing whitespace.

warning: 2 lines add whitespace errors.
Applying: XT-596 cli function improved and tested
/home/kamal/git/pgq/.git/rebase-apply/patch:32: trailing whitespace.

/home/kamal/git/pgq/.git/rebase-apply/patch:47: trailing whitespace.

/home/kamal/git/pgq/.git/rebase-apply/patch:53: trailing whitespace.

warning: 3 lines add whitespace errors.
Applying: XT-596 fix ask_confirmation in pgq purge
Applying: fixed TypeError: not all arguments converted during string formatting
Patch is empty.  Was it split wrong?
If you would prefer to skip this patch, instead run "git am --skip".
To restore the original branch and stop patching run "git am --abort". 
```

Enter fullscreen mode Exit fullscreen mode

如果我按照建议运行`git am --skip`,但是我在新的 repo 中还没有初始提交，我会得到这个错误:-

```
cat: /home/kamal/git/pgq/.git/ORIG_HEAD: No such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

通过添加初始提交，我可以通过它并重复运行`git am --skip`,直到应用了所有补丁。当然，产生的历史会有点奇怪，因为这个回购中的第一次提交似乎比原始历史的第一次提交要晚得多。

但除此之外，它似乎工作，我设法得到了所有历史完整的文件。如果你有更好的建议，欢迎在评论中分享。