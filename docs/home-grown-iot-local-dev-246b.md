# 本土物联网-本地开发

> 原文：<https://dev.to/azure/home-grown-iot-local-dev-246b>

既然我们已经开始构建我们的物联网应用程序,是时候开始讨论该应用程序的本地开发体验了。在一天结束时，我使用[物联网边缘](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-aapowell)在设备上进行部署，并管理与[物联网中心](https://docs.microsoft.com/en-us/azure/iot-hub/?WT.mc_id=devto-blog-aapowell)的通信，并且有一个非常全面的针对 [Visual Studio 代码](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-vs-code-develop-module?WT.mc_id=devto-blog-aapowell)和 [Visual Studio 2019](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-visual-studio-develop-module?WT.mc_id=devto-blog-aapowell) 的开发指南。其工作流程是创建一个新的物联网边缘项目，在您的计算机上设置物联网边缘，并以这种方式进行部署。这是我推荐你自己去做的方式，因为它给你生产和本地开发的最好的复制。

但是你可能已经猜到了，我**自己并没有**遵循那个指南，主要是因为我没有与物联网边缘(或物联网中心)集成，直到之后的*我才开始构建我的解决方案，相反，我将它们改造回一个标准。NET 核心项目，这就是我今天要讲的内容。*

## 定义我们的运动部件

我没有回去将物联网边缘完全集成到我的本地开发项目中的另一个原因是因为我有一个包含三个主要部分的 git repo，在 Raspberry Pi 上运行的下载器，一些在 Azure 中运行的 Azure 函数和一个 webserver，我将它用作我的 inverter API 的模拟，只用于支持本地开发。

这意味着我的 git 回购看起来像这样:

```
/src
    /Sunshine.Downloader
    /Sunshine.Functions
    /Sunshine.MockAPI 
```

Enter fullscreen mode Exit fullscreen mode

所有事情都假设你每次回购做一个项目，所以唯一的事情是物联网“东西”，这不是我的情况。

考虑到所有这些，拥有一个适合我的场景的本地开发设置比推荐指南中的 shoehorn 更容易。

## Docker 所有的东西

我非常喜欢使用 Docker 进行本地开发，鉴于物联网边缘部署使用 Docker 映像在设备上运行，我很早就决定以这种方式进行开发，这是一个非常方便的决定！但问题是，我需要运行 3 个不同的容器(*是的*我可以把它们都放在一个容器里，**不**你不应该这样做)，那么我们如何在 Visual Studio 代码中有效地做到这一点呢？一个`launch.json`文件倾向于围绕[调试](https://code.visualstudio.com/Docs/editor/debugging?WT.mc_id=devto-blog-aapowell)，所以我们必须坚持只使用[任务](https://code.visualstudio.com/Docs/editor/tasks?WT.mc_id=devto-blog-aapowell)。

### 建筑图像

这是我们需要做的第一件事，构建本地发展所需的三个不同的映像。但有趣的问题来了，是单。NET 核心解决方案，跨项目共享一些代码文件(主要是类型定义，这样我就可以通过网络进行类型确定)，这意味着我真的只想编译一次。这对于 Docker 来说有点痛苦，我通常会使用[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)并在那里执行编译步骤，用编译后的文件生成图像，但是当我生成三个图像时，这就不容易了！

为了解决这个问题，我首先在我的主机上执行编译(和发布)步骤，然后将构建工件放到映像中。这带来了一点开销，因为我必须在 VS 代码中手动运行一些任务。

### 编排任务

我在 VS 代码中使用了三个主要任务来本地运行。第一部礼部出版( [`publish:debug`](https://github.com/aaronpowell/sunshine/blob/c1005c8bf8ec1d295f05398556bd1bf8dccd7e36/.vscode/tasks.json#L34-L40) )的。NET 解决方案，所以我得到了要在 Docker 图像中使用的工件( [`docker build`](https://github.com/aaronpowell/sunshine/blob/c1005c8bf8ec1d295f05398556bd1bf8dccd7e36/.vscode/tasks.json#L109-L118) )，最后是一个创建三个图像的任务，最后是一个启动所有三个容器( [`docker run`](https://github.com/aaronpowell/sunshine/blob/c1005c8bf8ec1d295f05398556bd1bf8dccd7e36/.vscode/tasks.json#L119-L128) )的任务。你会在 GitHub 库中找到 [`tasks.json`](https://github.com/aaronpowell/sunshine/blob/c1005c8bf8ec1d295f05398556bd1bf8dccd7e36/.vscode/tasks.json) 。

所有这些任务都是[复合任务](https://code.visualstudio.com/Docs/editor/tasks?WT.mc_id=devto-blog-aapowell#_compound-tasks)，这意味着它们是运行其他任务的任务。关于复合任务需要记住的一点是，您在`dependsOn`属性中列出的任务是并行执行的，所以如果您想要一个运行图像的任务，它必须依赖于构建图像的任务，并且它依赖于。网络发布。这对我来说有点麻烦，因为我有 3 个任务(图像创建)依赖于 1 个任务的完成。NET publish)，所以我必须手动运行它们。

### 调试

我以前写过关于[调试的文章。VS Code](https://dev.to/azure/debugging-your-net-core-in-docker-applications-with-vs-code-3g5d)Docker 中的 NET Core 和我遇到的一个挑战[调试 Azure Functions 基础映像](https://dev.to/azure/fixing-issue-when-you-can-t-connect-to-docker-debugger-in-vs-code-34po)，这是我用于本地开发的过程，用任务启动容器，根据需要使用`launch.json`附加到容器。最大的痛苦是不能一次将调试器连接到多个容器，但这只是 VS 代码中调试器的一个限制(也不是主要的痛苦)。

## 结论

我为本地开发采取的方法并不真的与这个物联网项目相关，相反，它更多的是运行一些小的。NET 应用程序，都使用 Docker 容器。使用 Docker 意味着我可以轻松地控制我用于开发的环境，而且还可以复制项目的物联网部分在生产中的运行方式。

如果我正在构建一个项目，不仅在我自己的设备上运行(并且在团队环境中使用)，我会使用微软文档上描述的方法，用于 [Visual Studio 代码](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-vs-code-develop-module?WT.mc_id=devto-blog-aapowell)和 [Visual Studio 2019](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-visual-studio-develop-module?WT.mc_id=devto-blog-aapowell) ，因为它更加健壮。但是这个正好满足我的需求。😉