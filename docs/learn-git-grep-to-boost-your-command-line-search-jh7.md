# 学习 git-grep 来提高您的命令行搜索！

> 原文：<https://dev.to/iggredible/learn-git-grep-to-boost-your-command-line-search-jh7>

当从命令行搜索文件内容时，我们可能会想到`find`和`grep`，但是有多少人听说过`git grep`？

当在你的工作树中搜索时，我发现它比 find/ grep 更方便。下面是你如何开始使用 git-grep 的方法！

# 事情知道了

Git grep 用 Git 在项目内部搜索。唯一的要求是项目需要被跟踪(提示:`git add .`)。它无需提交就能工作。

# [t1 年“去抓”](#lets-git-greppin)

模式很简单。在你的项目目录中的任何地方做。

*   要搜索“foo”:

```
git grep foo 
```

Enter fullscreen mode Exit fullscreen mode

以下是其他有用的变体:

*   用行号搜索

```
git grep -n foo 
```

Enter fullscreen mode Exit fullscreen mode

*   仅返回文件名:

```
git grep -l foo 
```

Enter fullscreen mode Exit fullscreen mode

*   它与正则表达式兼容。

```
git grep "f[^\s]*\s" 
```

Enter fullscreen mode Exit fullscreen mode

*   要知道每个文件中有多少匹配项:

```
git grep -c foo 
```

Enter fullscreen mode Exit fullscreen mode

*   仅在特定文件扩展名下查找(忽略其他文件的匹配项)

```
git grep foo *.js 
```

Enter fullscreen mode Exit fullscreen mode

# 与/或

*   要搜索“foo”*或*“bar”，我们可以这样做:

```
git grep -e foo -e bar 
```

Enter fullscreen mode Exit fullscreen mode

*   搜索包含“foo”*和*“bar”并且在同一行上的*的文件:*

```
git grep -e foo --and -e bar 
```

Enter fullscreen mode Exit fullscreen mode

要搜索包含“foo”*和*“bar”的文件，不一定在同一行:

```
git grep --all-match -e foo -e bar 
```

Enter fullscreen mode Exit fullscreen mode

还有更多(勾选`man git-grep`，以上是我觉得有用的)。

# 发挥想象力:在提交内部和提交之间进行搜索

git grep 的真正强大之处在于它能够搜索我们想要的任何提交。

这里有两个我觉得非常有用的:

*   在特定提交中搜索

有两种方法:相对于 HEAD 引用它，引用它的 SHA。

```
git grep "foo" HEAD~1 // search ONLY in one commit before current head
git grep "foo" a1b2 // search ONLY in commit a1b2 
```

Enter fullscreen mode Exit fullscreen mode

*   在多个提交中搜索

它还接受多个提交引用。

```
git grep "foo" HEAD HEAD~1 HEAD~7 // search in current head, previous head, and 7th from head.
git grep "foo" a1b2 HEAD~5 // search inside a1b2 SHA and 5th from head 
```

Enter fullscreen mode Exit fullscreen mode

*   搜索两次提交之间的所有内容

这使用`git rev-list`命令。这里有一些快速刷新:

```
git rev-list HEAD~3..HEAD // Returns all commits between HEAD~3 and HEAD.
git rev-list --all // Returns ALL Commits from the dawn of time to present day. 
```

Enter fullscreen mode Exit fullscreen mode

结合 rev-list 和 git-grep，我们可以做到:

*   搜索 HEAD~3 和 HEAD 之间所有出现的“foo”

```
git grep foo $(git rev-list HEAD~3..HEAD) 
```

Enter fullscreen mode Exit fullscreen mode

*   搜索从最开始到当前出现的所有“foo”。

```
git grep foo $(git rev-list --all) 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们可以把学到的所有东西放在一起。这将从我们提交的每个文件中返回文件名，在每个文件中包含“bar”和任何匹配`f[^\s]*\s`模式的内容。

```
git grep --all-match -l -e "f[^\s]*\s" -e bar $(git rev-list --all) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！当我需要在工作树中搜索文件内容时，我发现 git grep 比传统的 grep 或 find 更方便。当然 find/grep 有 git grep 做不到的自己的用法，不过那是以后的事了。

# 资源

*   [git grep](https://remarkablemark.org/blog/2017/05/07/git-grep/)
*   [git-grep doc](https://git-scm.com/docs/git-grep) 的缩写
*   [像忍者一样搜索 git repo】](http://travisjeffery.com/b/2012/02/search-a-git-repo-like-a-ninja/)
*   [git grep 的乐趣](https://gitster.livejournal.com/27674.html)

黑客快乐！