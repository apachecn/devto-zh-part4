# 有用的 NPX

> 原文：<https://dev.to/samuelbeard/useful-npx-36fd>

如果您使用 Javascript，您可能会使用或者至少听说过`npx`。如果没有，就查一下。真的很方便。

下面是我用 npx 在 reg 上运行的一些命令:

* * *

```
npx okimdone npm install 
```

Enter fullscreen mode Exit fullscreen mode

如果你提高了音量，当你输入的命令完成时，会有一个声音告诉你。对于那些花费了很长时间却想回头再做的任务来说，这真的很方便。

* * *

```
npx npm-check

# Options
-u - Interactive Updates
-y - Update everything
-s - Skip unused
-g - Global 
```

Enter fullscreen mode Exit fullscreen mode

检查您安装的 npm 软件包，并为您提供有关它们的信息。这对于查看您的 package.json 中是否有您在应用程序中实际上没有使用的内容非常有用。

运行`npx npm-check -su`将简单地更新你所有的包。

* * *

```
npx npkill 
```

Enter fullscreen mode Exit fullscreen mode

从保存所有项目的文件夹中运行此程序。它将列出每个 node_modules 文件夹以及它所使用的磁盘空间。然后，您可以浏览它们，并通过按空格键逐个删除 node_modules 文件夹。

* * *

```
npx generact 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在为 react 创建组件，这将非常有用。

Generact 将复制您已经创建的组件，并给它一个新名称。它将复制旧的组件文件结构，并更改组件名称。

* * *

```
npx samuelbeard 
```

Enter fullscreen mode Exit fullscreen mode

只是觉得这很棒。

* * *

如果你知道更多非常有用的 npx 工具，请在评论中发表。