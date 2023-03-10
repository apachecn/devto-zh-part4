# 如何删除机器上所有 node_modules 文件夹，释放硬盘空间！

> 原文：<https://dev.to/trilon/how-to-delete-all-nodemodules-folders-on-your-machine-43dh>

> 原载于 2019 年 8 月 7 日 **[Trilon 博客](https://trilon.io/blog/how-to-delete-all-nodemodules-recursively)** 。

每当我们在一个新项目上工作时，我们都需要运行`npm install`，但是我们多久会想到这对我们的硬盘驱动器造成的损害呢？

<figure>

[![](img/75a1e959c6e1a2adf0faa2d05d121c7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IXLfDqOb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cnorkdsd8nmcse1erid7.png)

<figcaption>The dreaded "out of space" message</figcaption>

</figure>

一个 node_modules 文件夹可以占用 200 多兆字节的空间(有时是 1GB+！).

现在看看你的`github/`文件夹，或者你存储大部分项目的其他地方，你可以开始看到你的空闲硬盘空间的*一些*去了哪里！

我最近发了一篇关于这个的微博，因为这是我每隔几个月就会用到的东西，我想和其他可能遇到同样情况的人分享一下！

液体错误:内部

## 列出一个目录中找到的所有 node_modules:

首先，在我们真正开始删除 node_modules 之前，让我们看看目录中的所有 node _ modules！

> **注意**:确保你的光盘放在一个特定的目录下，那里有你大部分的项目。即:`documents`或`documents/github`。

### Mac / Linux:

这个命令将打印出每个文件夹，甚至显示文件夹占用了多少空间！感谢[押韵](https://dev.to/rhymes/comment/dpci)的提示！

```
$ cd documents
$ find . -name "node_modules" -type d -prune -print | xargs du -chs

--- Example output ---
1.0G ./Github/big-project/node_modules
225M ./Github/Trilon.io/node_modules
482M ./Github/Some_Demo/node_modules

1.7G total 
```

Enter fullscreen mode Exit fullscreen mode

### 窗口:

```
$ cd documents
$ FOR /d /r . %d in (node_modules) DO @IF EXIST "%d" echo %d" 
```

Enter fullscreen mode Exit fullscreen mode

这个列表会给你一个很好的主意，仅仅是你已经安装在你的机器上的项目数量！

* * *

## 删除一个目录中找到的所有 node_modules:

> **注意**:在这里要小心，确保你在一个目录中，在这个目录中你可以删除*所有*node _ modules 的实例，在删除之前运行上面的脚本来查看它们的完整列表。

这个脚本实际上与上面的非常相似，但是我们将利用`rm -rf`来完全删除它们。

> **警告:**此过程不可逆！

### Mac / Linux:

```
$ cd documents
$ find . -name 'node_modules' -type d -prune -print -exec rm -rf '{}' \; 
```

Enter fullscreen mode Exit fullscreen mode

### 窗口:

```
$ cd documents
$ FOR /d /r . %d in (node_modules) DO @IF EXIST "%d" rm -rf "%d" 
```

Enter fullscreen mode Exit fullscreen mode

#### Powershell 用户:

```
Get-ChildItem -Path "." -Include "node_modules" -Recurse -Directory | Remove-Item -Recurse -Force 
```

Enter fullscreen mode Exit fullscreen mode

### 有了！

对我个人来说，这通常会从我的硬盘中清除大约 40-60GB，但你的里程数可能会有所不同！

## 总之

*   在删除它们之前，确保列出给定目录*中的所有 node_modules。*
*   请务必小心，因为此过程*不可逆*！
*   记得重新开始你需要做的项目。
*   享受自由空间！:)

* * *

### 评论下面我很想听听你存了多少钱！

此外，不要忘记查看 [Trilon 博客](https://trilon.io/blog)获取更多深入的文章！