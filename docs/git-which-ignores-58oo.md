# 无视的饭桶

> 原文：<https://dev.to/serhatteker/git-which-ignores-58oo>

您刚刚开始了一个新项目，团队已经工作了一段时间，并且希望将您的代码添加到代码库中。或者您希望为您的新项目使用一个众所周知的 webapp 模板。

您编写了代码，进行了测试，然后添加了您的“神奇”文本，但它没有出现在`version control`中。`git status`不显示你的文件或目录。你也不能手动检查`.gitignore`，因为它有 450 行长。

解决办法？使用内置 git 命令`check-ignore` :

```
$ git check-ignore -v filename 
```

Enter fullscreen mode Exit fullscreen mode

比如说；您为一些自动化编写了`bash`脚本，并将它们添加到项目根目录中名为`scripts`的目录中。当你使用`check-ignore`时，它会给出如下输出:

```
.gitignore:274:[Ss]cripts       scripts 
```

Enter fullscreen mode Exit fullscreen mode

基本上是这样说的:在“274”`[Ss]cripts`行中，会导致忽略您的`scripts`目录。

完成了。