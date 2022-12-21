# 降低 VSCode 内存消耗

> 原文：<https://dev.to/claudiodavi/reducing-vscode-memory-consumption-527k>

# 如何减少 VSCode 的内存使用

我使用 VSCode 已经有一段时间了，我注意到的最烦人的事情之一是它占用了多少内存，特别是与 Sublime Text 相比。

我将向您介绍一些我发现的技巧，至少可以使它适用于大型项目。

免责声明:我主要使用 Python，因此我确信你会找到更多的选项来优化 Javascript 或你喜欢的语言。

下面的大部分提示必须放在你的用户设置(JSON)中

### 遥测

首先，你知道 VSCode 向微软发送关于它的使用的数据吗？
如果你想关机很简单，把

`"telemetry.enableTelemetry": false`

放入你的配置中，你就一切就绪了。

### 搜索索引

搜索是 VSCode 最消耗内存的活动之一。它必须保存所有文件及其内容的索引。您可能不想在 node_modules/或 env/文件夹中搜索，对吗？
我以前也遇到过这个问题，我不知道它们现在是否被默认禁用，但它在我的配置文件中，所以是这样的:

```
"search.exclude":  {  "**/node_modules":  true,  "**/bower_components":  true,  "**/env":  true,  "**/venv":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

### 文件观察者

文件监视器用于检测工作文件和文件夹中的变化。如果你像我一样，每一个新的包你都必须在运行中完成，那么你的二级文件和文件夹可能会有很多变化。
因此，我们将禁用这些文件夹和任何其他我们不想遵循的观察器，如我们的 git/objects 文件夹。

```
 "files.watcherExclude":  {  "**/.git/objects/**":  true,  "**/.git/subtree-cache/**":  true,  "**/node_modules/**":  true,  "**/env/**":  true,  "**/venv/**":  true,  "env-*":  true  }, 
```

Enter fullscreen mode Exit fullscreen mode

### 组织你的资源管理器

好了，我们已经针对性能进行了优化，现在我们要针对生产率进行优化。要做的最重要的事情之一是减少你工作空间的混乱。

为此，我们将从 Explorer 选项卡中删除文件。为什么？我们真的不想看到我们不打算使用的文件。

```
"files.exclude":  {  "**/.git":  true,  "**/.DS_Store":  true,  "**/.vscode":  true,  "**/__pycache__":  true,  "**/.pytest_cache":  true,  "**/node_modules":  true,  "venv":  true,  "*.sublime-*":  true,  "env*":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

这将让你远离大部分杂乱的东西。这太好了！

### 额外提示

一些额外的提示可以帮助你:

#### 工作区

工作空间很棒，创建几个吧。我总是为同一个项目打开几个 VSCode 实例，如果你在做全栈，用一个做后端，另一个做前端开发。如果你像我一样正在构建微服务，你可以为每个服务使用一个工作空间。你会看到它变得多干净。

#### 分机

尽可能保留最少的扩展，大多数都没有优化。保留您每天使用的内容，禁用或卸载其他内容。我每月检查一次我的分机，做一次玛丽-近藤式的清理。

#### 完整脚本

```
"files.exclude":  {  "**/.git":  true,  "**/.DS_Store":  true,  "**/.vscode":  true,  "**/__pycache__":  true,  "**/.pytest_cache":  true,  "**/node_modules":  true,  "node_modules":  true,  "venv":  true,  "*.sublime-*":  true,  "env*":  true  },  "search.exclude":  {  "**/node_modules":  true,  "**/bower_components":  true,  "**/env":  true,  "**/venv":  true  },  "files.watcherExclude":  {  "**/.git/objects/**":  true,  "**/.git/subtree-cache/**":  true,  "**/node_modules/**":  true,  "**/env/**":  true,  "**/venv/**":  true,  "env-*":  true  }, 
```

Enter fullscreen mode Exit fullscreen mode

* * *

你还有其他可以提高 VSCode 性能的技巧吗？与我们分享！