# 在代码中使用 shell/bash 命令？使用“长”选项

> 原文：<https://dev.to/rgeraldporter/using-shell-bash-commands-in-code-use-long-options-2f4k>

许多开发人员有时需要在代码中使用 bash 或 shell 脚本。

可能是 Docker 中发出的命令，或者是 Node.js 中的 exec 函数，或者是 Jenkins 管道中的 shell 命令。

一个简短而简单的提示是，当在代码中记录命令时，总是使用可用的长选项。

长(`--`)和短(`-`)选项的存在是有原因的——一个是为了文档的清晰，另一个是为了在终端中输入命令时的简洁。

例如，下面是一个 docker 命令示例。假设您在代码中发现了这个:

```
docker build --no-cache -t myimage:latest -f path/Dockerfile -q . 
```

假设您是 docker 新手，在 Jenkinsfile 或 bash 脚本中遇到了这个问题。

看起来很清楚它在做什么。但是其他的呢？

如果这样写不是更好吗:

```
docker build --no-cache --tag myimage:latest --file path/Dockerfile --quiet . 
```

更好的是，如果它被分成一个更长的语句，对每个选项使用换行符会怎么样？

```
docker build \
    --no-cache \
    --tag myimage:latest \
    --file path/Dockerfile \
    --quiet \
    . 
```

现在，下一个读到这个代码的人不太可能有去查`-q`、`-f`和`-t`是干什么的冲动。

并非所有的终端应用程序都与它们的短选项的含义一致，很容易将它们混淆。通常情况下，`-q`在一个应用程序中意味着`--quiet`,而在另一个应用程序中意味着`--query`。

做到这一点，您的代码审查者和继承者可能会非常感谢您。