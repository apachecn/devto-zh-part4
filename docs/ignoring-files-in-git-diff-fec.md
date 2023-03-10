# 忽略 Git Diff 中的文件

> 原文：<https://dev.to/swelham/ignoring-files-in-git-diff-fec>

最近在运行 git diff 时，我注意到我的 difftool 卡住了。我发现有一个包含大型二进制文件的文件导致了这个问题。我不需要看到这个文件中的变化，所以我开始寻找忽略它的方法。

原来有一种叫做 pathspec 的简便语法，可以用来过滤掉文件和目录。

这被直接传递到 diff 命令中。

```
$ git difftool origin/master ':(exclude)test_data/data.json' 
```

这样一来，`data.json`文件将被排除在 diff 之外，允许我的 difftool 轻松处理其余的文件。

Pathspecs 也可以与其他 git 命令一起使用。要查看使用它们的其他方法，请查看 git 术语表的 [pathspec 部分](https://git-scm.com/docs/gitglossary#Documentation/gitglossary.txt-aiddefpathspecapathspec)。