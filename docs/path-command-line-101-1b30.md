# 关于 PATH 命令行，您只需要知道两件事

> 原文：<https://dev.to/iggredible/path-command-line-101-1b30>

> “当我年轻的时候，在正确的道路上行走有点困难。”——道恩·强森

[![dwayne](img/da2dcc1dfe13af61cdb5f9caf631e0b5.png)](https://i.giphy.com/media/kNA1sKSqxgFDq/giphy.gif)

如此智慧。不是所有的路都通向幸福。错误的`PATH`会让你不快乐。在这里我们将学习正确的道路并坚持下去！

关于 path，你可以了解很多东西。我认为最重要的两个是:

1.  寻找你的道路
2.  更新您的路径。

# 寻找你的道路

在 mac 中，您可以通过键入`echo $PATH`从命令行找到路径。我的大概是这样的:

```
echo $PATH
/Users/iggy/.nvm/versions/node/v10.15.1/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin 
```

Enter fullscreen mode Exit fullscreen mode

路径由冒号(`:`)分隔，从左向右读取。

例如，如果我执行`node`，我的终端将首先在(`/Users/iggy/.nvm/versions/node/v10.15.1/bin`)搜索节点*可执行文件*，然后(`/usr/local/bin`，等等。如果在任何地方都没有找到节点，它将返回`command not found: node`。

要找到节点当前使用的路径，运行`which node`，在我的例子中，我看到:

```
/Users/iggy/.nvm/versions/node/v10.15.1/bin/node 
```

Enter fullscreen mode Exit fullscreen mode

注意我的一个路径和节点路径的相似之处:

```
#PATH
/Users/iggy/.nvm/versions/node/v10.15.1/bin 

#node path
/Users/iggy/.nvm/versions/node/v10.15.1/bin/node 
```

Enter fullscreen mode Exit fullscreen mode

# 更新您的路径

你可以*预先计划*或者*追加*你的路径

```
PATH=/your/new/prepend/path:$PATH
PATH=$PATH:/your/new/append/path 
```

Enter fullscreen mode Exit fullscreen mode

这种变化是暂时的。当终端关闭时，它将消失。为了使它永久，更新路径在`.bash_profile`或`.profile`

```
export PATH="~/new/path:$PATH" 
```

Enter fullscreen mode Exit fullscreen mode

# 应用:我们来黑一条路吧！

假设你是一个邪恶的人，想要修改你同事的`node`命令，所以当他们运行`node`时，他们正在运行你的脚本。你所需要的是预先计划好你自己的路径，这样当他们运行`node`时，路径将首先执行你的节点可执行文件。你可以这样做:

创建`/for-fun`目录，在里面创建一个名为`node`的文件。确保在第一行添加`#!/bin/bash`(shebang):

```
#!/bin/bash
echo "ALL YOUR FILES ARE GONE *EVIL LAUGHS*" 
```

Enter fullscreen mode Exit fullscreen mode

保存，然后授予权限`chmod +x ./node`。需要添加 shebang 和权限，以便他们可以直接运行`node`而不是`./node`

前置路径:

```
PATH=/Users/iggy/for-fun:$PATH 
```

Enter fullscreen mode Exit fullscreen mode

(用您使用的路径替换`Users/iggy/for-fun`。如果你不确定你在哪里，你可以使用`pwd`

检查新添加的路径(`echo $PATH`)，确保我们的前置路径是第一个显示的路径。还要检查您的节点路径(`which node`)——您应该会看到更新后的路径。

酷！下次有人运行`node`，就会看到:

```
node
ALL YOUR FILES ARE GONE *EVIL LAUGHS* 
```

Enter fullscreen mode Exit fullscreen mode

这是所有的乡亲。黑客快乐！！