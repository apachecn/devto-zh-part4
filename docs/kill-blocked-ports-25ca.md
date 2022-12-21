# 终止被阻止的端口

> 原文：<https://dev.to/eclecticcoding/kill-blocked-ports-25ca>

所以你正在努力工作在你的新的 [Gatsby](https://www.gatsbyjs.org/) 网站上，启动开发服务器。

**失败！**。您将看到控制台错误消息:

```
Something is already running at port 8000
Would you like to run the app at another port instead? [Y/n] 
```

Enter fullscreen mode Exit fullscreen mode

原因是进程没有完全关闭，或者您没有退出命令就终止了终端窗口。

## 做什么

仅供参考。以下修复适用于 MacOS 或 Ubuntu/Linux。它可能在 Windows 上工作。

解决方法很简单。

*   退出启动。
*   在命令提示符下输入以下内容:

`kill -9 $(lsof -t -i:8000)`

冷却全部固定。但是，您希望每次发生这种情况时都搜索出该命令吗？

**没有**，当然没有。

**解决方案**:在自己喜欢的终端中创建一个命令别名。在 [ZSH](https://ohmyz.sh/) 中打开你的`zshrc`文件，添加如下别名:
`alias k8="kill -9 $(lsof -t -i:8000)"`

现在，下一次发生这种情况时，退出开发脚本并进入`k8`。完成了。

## 都是固定的，排序的。

alias 命令只响应一种情况和一个端口号。Gatsby serve 命令(端口 9000)，或者 Create-React-App(端口 3000)呢？您必须为每种情况创建一个别名。一定有更有效的方法。

答案是创建一个 shell 脚本。
创建下面的脚本:

```
#!/bin/bash

#styles
VP_RED='\033[01;31m'

# Update default core install
echo -e "${VP_RED}KILLING the SPECIFIED PORT"
kill $(lsof -t -i:$1) 
```

Enter fullscreen mode Exit fullscreen mode

有几点需要注意:

*   样式行`VP_RED='\033[01;31m'`仅在终端窗口中使命令变成红色。
*   `echo`行启动样式。
*   最重要的部分是最后一行，这是杀死端口的命令。
*   文件名将是您键入的命令。就我而言，`kport`。
*   使该文件可执行:`chmod +x kport`
*   放置在用户的路径中。

在我的例子中，我已经添加了`/bin`，并将文件命名为`kport`。
所以，当你执行这个文件时，记得包含一个你想要终止的端口号，作为一个参数(例如:
`kport 8000`

搞定了。我希望这有所帮助。祝你愉快。