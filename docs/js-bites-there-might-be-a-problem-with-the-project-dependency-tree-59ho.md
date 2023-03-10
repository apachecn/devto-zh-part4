# JS Bites:项目依赖树可能有问题。

> 原文：<https://dev.to/ranewallin/js-bites-there-might-be-a-problem-with-the-project-dependency-tree-59ho>

有时，当你试图安装一个 React 应用程序时，你可能会得到一个长长的错误，开始是这样的:

> 项目依赖关系树可能有问题。这
> 
> 可能不是 Create React 应用程序中的错误，而是您需要在本地修复
> 
> 的问题。

你可能还会看到以下几点建议:

> 如果希望忽略此检查，请将 SKIP_PREFLIGHT_CHECK=true 添加到。项目中的 env 文件。这将永久禁用它

## 那么，这意味着什么呢？

最有可能的原因是您不小心将一个包安装到了项目文件夹上面的文件夹中。例如，如果您在`/home/projects/my-project`中，您可能在某个时候不小心将一个包安装到了`/home/projects`中。如果您在命令行上并试图在切换到项目目录之前安装一个包，这很容易做到。

要解决这个问题，请将`cd ../`放入上面的目录，然后执行`ls`。你是否看到放置错误的`node_modules`和`package.json`？如果有，两个都删除。否则，`cd ../`又要检查一遍。继续这样做，直到找到错误的安装。

一旦删除了错误的安装，返回到您的项目目录，删除那里的 node_modules，然后再次尝试安装。

* * *

```
 JS Bites

 Have you ever need a quick solution to a problem, but when you search 
 Google you are met with pages of tutorials and detailed instructions that 
 bury the information you need? That's what JS Bites attempts to solve. 
 Each post is a small, bite-sized primer on a very specific JS topic. The 
 aim is to provide enough detail to boost understanding, but not so much that 
 you become overwhelmed or lost. 
```

Enter fullscreen mode Exit fullscreen mode