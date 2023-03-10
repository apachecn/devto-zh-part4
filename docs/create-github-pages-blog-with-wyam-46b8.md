# 用 Wyam 创建 GitHub Pages 博客

> 原文：<https://dev.to/jonathanhiggs/create-github-pages-blog-with-wyam-46b8>

## 简介

这是一个建立博客网站的指南，该网站由 [github pages](https://pages.github.com) 托管，并使用 [wyam](https://wyam.io) 生成静态内容。它是基于一个[老向导](https://win32.io/posts/Wyam-GitHub-Pages)的，该向导在执行步骤时出现了一些问题。本指南修正了这些步骤并简化了流程

总体策略是用它来创建一个名为`<username>.github.io`的 github 库，发布到同一个 url 上。只有主分支中的 html 内容将被发布，因此将会有第二个孤立分支，它将包含所有的源 markdown 和配置文件。然后通过使用 wyam 输出中的`git subtree split`命令来填充主分支

首先要做的是创建一个文件夹并初始化 git 存储库

```
$ cd /path/to/dev-stuff
$ mkdir <username>.github.io
$ cd <username>.github.io
$ git init 
```

这将初始化主分支，它应该只包含 wyam 输出，所以立即切换到其他分支，这里称为`dev`

```
$ git checkout --orphan dev 
```

在这个阶段添加`.gitignore`也是一个好主意，这样可以确保只有正确的文件被添加到存储库中。请注意，输出文件夹没有被添加到 ignores 中，而是将保持在源代码控制下

```
# Visual Studio
.vs/

# Cake build
tools/
!tools/packages.config

# Wyam
wwwroot/
config.wyam.hash
config.wyam.dll 
```

简单说明一下，我使用 VisualStudio 和一个名为 [MarkdownEditor](https://github.com/madskristensen/MarkdownEditor) 的扩展来呈现 markdown 内容，这就是为什么`.vs/`被排除在忽略之外。这根本不是必需的，但是值得为任何可能创建的编辑器特定文件添加 ignore

## 设置博客

Wyam 是生成静态 web 内容的优秀工具。它都是用 dotnet 编写的，高度模块化，所以你可以相当容易地扩展它，但也有预烤食谱。我们将使用博客食谱，这将为我们提供一个将 markdown 文件转化为完整博客网站的渠道

安装 wyam 最简单的方法是通过如下的 dotnet cli，其他方法可以在 [wyam 网站](https://wyam.io/docs/usage/obtaining)
找到

```
$ dotnet tool install -g Wyam.Tool 
```

Wyam 附带了一个包含 wyam 模块管道的[博客配方](https://wyam.io/recipes/blog/)。这个配方是用下面的命令初始化的，这个命令将从 nuget 下载它需要的额外工具，创建一些配置文件，以及一个名为 input 的文件夹，所有的源减价页面都存储在这个文件夹中

```
$ wyam new -r Blog 
```

从输入文件中，`wyam build`命令将执行静态生成管道，并在输出文件夹中创建 html 页面。对于一个大部分是空白的项目来说，这可能需要一些时间，而当有很多页面要生成时，这可能需要几分钟

```
$ wyam build 
```

包括一个具有标准文件监视和热重载的开发服务器，允许在本地主机服务器上预览站点

```
$ wyam preview 
```

## 蛋糕打造

Cake 是一个非常棒的构建自动化工具，它使用 C#脚本文件来定义构建过程，并使用一些罗斯林魔法来编译和运行它们。它非常容易扩展，并且有一组广泛的插件，涵盖了所有对构建有用的工具，包括 wyam

首先要做的是[下载 powershell 引导程序](https://cakebuild.net/docs/tutorials/setting-up-a-new-project),当它运行时，将下载蛋糕可执行文件并执行蛋糕脚本

接下来是蛋糕构建脚本本身，它被分成几个子部分。第一部分做了两件事，首先，它使用预处理器指令通知 cake 应该下载并加载`Cake.Wyam` nuget 包，以便在脚本中使用。其次，它获得命令行目标参数，该参数将用于设置哪个任务将被调用

```
#addin nuget:?package=Cake.Wyam

var target = Argument("target", "Deploy"); 
```

接下来是最有趣的部分。它定义但不执行任务。前两个任务非常简单，要么运行`Build`要么运行`Preview`服务器(包括一个构建)。这些调用了`Wyam()`方法，这是从第一部分中的`Cake.Wyam` nuget 包导入的蛋糕扩展，并使用了第二部分中创建的设置对象

最后的`Deploy`任务是`git subtree split`魔法发生的地方。`Deploy`依赖于`Build`，所以输出目录会用最新生成的文件更新。这些文件被提交给 git，然后`subtree split`会过滤掉所有的修改和文件，除了输出文件夹中的文件，然后把这些新的提交放到主分支(省略。路径中的/output 目录)。这样做的结果是，主分支拥有生成的(并且只有生成的)输出的完整版本化历史，这正是 github pages 想要的

```
Task("Build")
    .Does(() => Wyam());

Task("Preview")
    .Does(() => Wyam(new WyamSettings {
        Preview = true,
        Watch = true,
    }));

Task("Deploy")
    .IsDependentOn("Build")
    .Does(() => {
        StartProcess("git", "add .");
        StartProcess("git", "commit -m \"Output files generated for subtree\"");
        StartProcess("git", "subtree split --prefix output -b master");
        StartProcess("git", "push -f origin master");
    }); 
```

最后一步是一个方法调用，告诉 cake 运行哪个任务。在这种情况下，`target`将默认为`Deploy`，除非在调用脚本时设置了参数。Cake 将找到指定的任务，并使用依赖信息创建一个需要运行的任务树来执行所有需要的任务

```
RunTarget(target); 
```

### 运行构建

脚本的执行相当容易。从命令提示符并假设 powershell 在路径
上

```
$ powershell ".\build.ps1" 
```

运行预览服务器是通过设置`target`参数并传入预览任务名称
来完成的

```
$ powershell ".\build.ps1" -target Preview 
```

注意:cake 工具和插件的版本并不总是匹配的，因此您可能需要添加一个额外的参数来消除版本不匹配警告。忽略该警告可能会导致运行脚本时出现问题，但大多数情况下是没问题的

```
$ powershell ".\build.ps1" --settings_skippackageversioncheck=true 
```

## GitHub 资源库

最后一步是在 github 上创建一个存储库，并推送初始内容。为了让 github 理解它是用于页面的，存储库必须被命名为`<username>.github.io`，并且您可能还需要在存储库设置页面的“GitHub 页面”部分启用它

现在需要做的就是在本地存储库中用 https 或 ssh uri(都显示了)将它设置为源远程(T0)

```
$ git remote add origin https://github.com/<username>/<username>.github.io.git
$ git remote add origin git@github.com:<username>/<username>.github.io.git 
```

现在，cake build 会将输出推送到 github，几秒钟后，内容就会出现在万维网上

* * *

参见下一篇[帖子](//./complete-cake-script-for-wyam)以获得使用 wyam 的完整蛋糕构建脚本