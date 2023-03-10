# Wyam 的完整蛋糕脚本

> 原文：<https://dev.to/jonathanhiggs/a-complete-cake-script-for-wyam-d5p>

## 简介

之前的[帖子](https://dev.to/posts/create-github-pages-with-wyam)给出了创建博客网站的指导，其中包括一个简单的蛋糕制作脚本。这篇文章将采用这个脚本，提取一些隐含的参数，并添加一些额外的步骤，以达到清晰和实用的目的

*跳转到帖子末尾查看完整脚本*

## 原剧本

首先，这是上一篇文章的简单脚本。目前，输出目录和将要部署的分支有隐式参数

```
#addin nuget:?package=Cake.Wyam

var target = Argument("target", "Deploy");

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

RunTarget(target); 
```

像任何函数一样，最好在脚本开始时指定所有可能变化的参数。虽然在这个脚本中“参数”被硬编码为一个特定的值最初看起来很奇怪，但是提取它们是很有用的，这样脚本可以被复制粘贴并在另一个项目中重用，只需在脚本中的一个地方设置参数。目前，输入和输出目录，以及被部署的分支在所有步骤
中都被隐式使用

```
var deployBranch = "master";
var output = "output"; 
```

现在，参数被显式地设置为变量，它们的所有实例都可以在脚本的其余部分使用一点字符串插值
来替换

```
Task("Deploy")
    .IsDependentOn("Build")
    .Does(() => {
        StartProcess("git", "add .");
        StartProcess("git", "commit -m \"Output files generated for subtree\"");
        StartProcess("git", $"subtree split --prefix {output} -b {deployBranch}");
        StartProcess("git", $"push -f origin {deployBranch}");
    }); 
```

与参数类似，目录和文件的位置可以提取和设置一次。这个脚本还没有使用这些路径，但是很快就会使用了。同样，路径可以构建在彼此之上，因此路径的每个部分只在一个地方指定

```
var root = MakeAbsolute(Directory("."));
var inputDirectory = Directory($"{root}/input");
var outputDirectory = Directory($"{root}/{output}"); 
```

继续将所有配置移到脚本开始的主题，传递到 wyam 的设置对象是在使用时创建的，并且包含隐式参数。它们被移动到脚本的开头，路径可以在
中传递

```
var build = new WyamSettings {
    InputPaths = new [] { inputDirectory.Path },
    OutputPath = outputDirectory
};

var preview = new WyamSettings {
    InputPaths = new [] { inputDirectory.Path },
    OutputPath = outputDirectory,
    Preview = true,
    Watch = true,
};

Task("Preview")
    .Does(() => Wyam(preview));

Task("Build")
    .Does(() => Wyam(build)); 
```

## 蛋糕。Git 加载项

该脚本目前使用进程外调用来与 git 交互，但是有一个`Cake.Git`插件，其中包含许多常见 git 命令的方法。具体来说，`GitCommit`方法采用额外的参数来设置将用于提交的用户名和电子邮件。这对于在构建过程中创建提交的脚本非常有用。这里参数被提取到脚本的开头，另一个`gitMessage`参数用于消息

```
var gitMessage = Argument("message", "Output files generated for subtree");

var gitUser = "My Name";
var gitEmail = "myname@site.com";

Task("Deploy")
    .IsDependentOn("Build")
    .Does(() => {
        GitAddAll(root);
        GitCommit(root, gitUser, gitEmail, gitMessage);

        StartProcess("git", $"subtree split --prefix {output} -b {deployBranch}");
        StartProcess("git", $"push -f origin {deployBranch}");
    }); 
```

不幸的是，git 插件不包含任何用于`subtree`的 cake 别名，所以该步骤仍然需要调用进程外。另外，`Cake.Git`使用的 ssh 库有一个长期存在的问题，这意味着如果存储库被设置为 ssh url，push 和 pull 将无法工作。我希望有一天这两个问题都能得到解决

git 消息仍将默认为与原始脚本相同的值，但是也可以通过将它传递给脚本
来覆盖它

```
$ powershell ".\build.ps1" --message="I did a thing" 
```

## 发布工作流

这个脚本的主要功能变化是创建一个工作流来处理文章，但在文章准备好之前不发布它们。这将通过为 WIP 帖子创建一个新的帖子目录来实现，包括在预览中，但不包括在主构建中，并添加一个任务，当提供有效的帖子名称时，该任务将移动帖子

要做的第一件事是创建一个工作进行中的目录，它将在预览中可见，但不会发布。wyam 设置允许将多条路径传递给参数`InputPaths`，当创建静态页面
时，Blog recipe 将查看 posts 子目录

```
/input
    /posts
/output
/wip
    /posts
... 
```

这两个目录的路径变量稍后会用到，所以它们会和其他路径声明一起添加到脚本的开头。构建设置不需要改变，但是 wip 目录被传递到预览设置中，以便在预览内容生成中包含 wip 帖子

```
var postsDirectory = Directory($"{root}/input/posts");
var wipDirectory = Directory($"{root}/wip/posts");

var build = new WyamSettings {
    InputPaths = new [] { inputDirectory.Path },
    ...
};

var preview = new WyamSettings {
    InputPaths = new [] { inputDirectory.Path, wipDirectory.Path },
    ...
}; 
```

Publish 是一个新任务，它将文章从 wip 目录移动到`input/posts`目录。首先需要的是一个脚本参数，帖子的名称将被传递到该参数中。在这里，文章是按年份组织的，因此脚本可以确保在执行实际移动之前，包含当前年份的文章子目录存在。文件路径是根据路径声明构建的，这有助于使脚本可读。还要注意，为了简洁起见，参数中省略了`.md`扩展名，任务添加了

```
var post = Argument("post", string.Empty);

Task("Publish")
    .Does(() => {
        EnsureDirectoryExists($"{postsDirectory}/{DateTime.Now.Year}");

        MoveFile(
          File($"{wipDirectory}/{post}.md"),
          File($"{postsDirectory}/{DateTime.Now.Year}/{post}.md")
        );
    }); 
```

Wyam 博客帖子有一个发布的文档元数据字段，可以在移动过程中自动设置。`Cake.FileHelpers`是另一个插件，它有`ReplaceRegexInFiles`方法来完成这个

```
#addin nuget:?package=Cake.FileHelpers

Task("Publish")
    .Does(() => {
        ReplaceRegexInFiles(
            $"{wipDirectory}/{post}.md",
            "Published:\\s*\\d{4}-\\d{2}-\\d{2}",
            $"Published: {DateTime.Now.Date.ToString("yyyy-MM-dd")}"
        );
        ...
    }); 
```

到目前为止，这个任务的唯一问题是，它需要在每次构建时移动一个 post，但情况并不总是这样。Cake 允许有条件地执行任务，而不会阻塞后续任务的执行，所需要做的就是检查是否已经将 post 传递到脚本中来实现这个

```
Task("Publish")
    .WithCriteria(!string.IsNullOrEmpty(post))
    .Does(() => {
        ...
    }); 
```

最后，传递给 git 的消息可以用 post 的名称
进行修改

```
Task("Publish")
    .Does(() => {
        ...
        gitMessage = $"Published: {post}";
    }); 
```

假设在 wip/posts 目录中有一个名为`my-post.md`的文件，那么下面的命令将利用 Publish 任务将它从 wip 移动到 inputs 目录中，因此它将被发布到主站点。需要注意的一点是，在命令行参数解析中似乎有一个错误，当参数值中出现破折号时，它会假设第二个脚本名称被传入，因此该值必须放在双引号中

```
$ powershell ".\build.ps1" --post="my-post" 
```

## 信息任务

添加的最后一步并没有进行功能上的更改，只是简单地转储出参数和变量。一般来说，当将脚本移植到一个新项目并在第一次不太成功时进行调试时，或者如果脚本是由 CI 流程执行而不是在本地执行时，这可能非常有用

```
Task("Info")
    .Does(() => {
        Information("Parameters:");
        Information($"-target {target}");
        Information($"-post {post}");
        Information("\nPaths:");
        Information($"Root Directory {root}");
        Information($"Output Directory {outputDirectory}");
        Information($"Post Directory {postsDirectory}");
        Information($"WIP Directory {wipDirectory}");
        Information($"\nGit Settings:");
        Information($"Git User {gitUser}");
        Information($"Git Email {gitEmail}");
    }); 
```

## 最终脚本

将所有这些放在一起，这是最终的脚本

```
////////////////////////
// Add-ins & Tools
//////////////////////

#addin nuget:?package=Cake.FileHelpers
#addin nuget:?package=Cake.Git
#addin nuget:?package=Cake.Wyam

////////////////////////
// Parameters
//////////////////////

var target = Argument("target", "Deploy");
var post = Argument("post", string.Empty);
var gitMessage = Argument("message", "Output files generated for subtree");

var deployBranch = "master";
var gitUser = "My Name";
var gitEmail = "my.name@site.com";

var output = "output";

var root = MakeAbsolute(Directory("."));
var inputDirectory = Directory($"{root}/input");
var postsDirectory = Directory($"{root}/input/posts");
var outputDirectory = Directory($"{root}/{output}");
var wipDirectory = Directory($"{root}/wip/posts");

////////////////////////
// Settings
//////////////////////

var build = new WyamSettings {
    InputPaths = new [] { inputDirectory.Path },
    OutputPath = outputDirectory
};

var preview = new WyamSettings {
    InputPaths = new [] { inputDirectory.Path, wipDirectory.Path },
    OutputPath = outputDirectory,
    Preview = true,
    Watch = true,
};

////////////////////////
// Tasks
//////////////////////

Task("Info")
    .Does(() => {
        Information("Parameters:");
        Information($"-target {target}");
        Information($"-post {post}");
        Information("\nPaths:");
        Information($"Root Directory {root}");
        Information($"Output Directory {outputDirectory}");
        Information($"Post Directory {postsDirectory}");
        Information($"WIP Directory {wipDirectory}");
        Information($"\nGit Settings:");
        Information($"Git User {gitUser}");
        Information($"Git Email {gitEmail}");
    });

Task("Preview")
    .IsDependentOn("Info")
    .Does(() => Wyam(preview));

Task("Publish")
    .WithCriteria(!string.IsNullOrEmpty(post))
    .IsDependentOn("Info")
    .Does(() => {
        ReplaceRegexInFiles(
            $"{wipDirectory}/{post}.md",
            "Published:\\s*\\d{4}-\\d{2}-\\d{2}",
            $"Published: {DateTime.Now.Date.ToString("yyyy-MM-dd")}"
        );

        EnsureDirectoryExists($"{postsDirectory}/{DateTime.Now.Year}");

        MoveFile(
          File($"{wipDirectory}/{post}.md"),
          File($"{postsDirectory}/{DateTime.Now.Year}/{post}.md")
        );

        gitMessage = $"Published: {post}";
    });

Task("Build")
    .IsDependentOn("Publish")
    .Does(() => Wyam(build));

Task("Deploy")
    .IsDependentOn("Build")
    .Does(() => {
        GitAddAll(root);
        GitCommit(root, gitUser, gitEmail, gitMessage);

        StartProcess("git", $"subtree split --prefix {output} -b {deployBranch}");
        StartProcess("git", $"push -f origin {deployBranch}");
    });

////////////////////////
// Invoke
//////////////////////

RunTarget(target); 
```