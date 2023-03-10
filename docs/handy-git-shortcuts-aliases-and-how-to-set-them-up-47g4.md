# 便捷的 Git 快捷方式(别名)以及如何设置它们

> 原文：<https://dev.to/morinoko/handy-git-shortcuts-aliases-and-how-to-set-them-up-47g4>

如果您正在使用 git，这些方便的短代码将加快您的工作流程。以下是 git 网站上推荐的基本缩写 git 命令。当我需要在新电脑上设置 git 时，我总是忘记如何做，所以把这个贴出来作为参考。

*   `co`用于结账
*   `br`为分支
*   `ci`对于提交
*   `st`为状态

## 如何设置

要设置别名，您可以使用带有- global 标记的 git config 命令，以便它适用于您的所有 git 存储库。

```
$ git config --global alias.<YOUR CHOSEN SHORTCUT> <THE ORIGINAL GIT COMMAND> 
```

Enter fullscreen mode Exit fullscreen mode

因此，要设置我上面提到的基本快捷方式，请打开您的终端并输入以下内容:

```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status 
```

Enter fullscreen mode Exit fullscreen mode

现在你一切都准备好了！可以用`co`代替`checkout`等等。例如，对于`git commit`，你现在可以这样做:

```
git ci -m "add CSS styles" 
```

Enter fullscreen mode Exit fullscreen mode

见官方 git 文档[这里](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases)。