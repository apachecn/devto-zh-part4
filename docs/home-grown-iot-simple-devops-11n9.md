# 自制物联网-简单开发

> 原文：<https://dev.to/azure/home-grown-iot-simple-devops-11n9>

[应用程序正在构建](https://dev.to/aaronpowell/home-grown-iot-data-downloader-56nc)，我们的[数据可以被处理](https://dev.to/azure/home-grown-iot-processing-data-5cnm)，剩下的就是部署好一切，猜猜这篇文章的主题是什么！

当谈到物联网项目的 CI/CD(持续集成/持续部署)时，可能会感觉有点令人生畏，你已经有了这个小小的计算机，你正试图将东西放在上面，它不仅仅是你的目标“云”。而且这些设备不像普通电脑，它们有 ARM32 这样怪异的芯片组！

## 部署到树莓派

我们需要想办法把我们的下载器放到树莓派上。正如我在关于[下载器如何工作](https://dev.to/aaronpowell/home-grown-iot-data-downloader-56nc)的帖子中提到的，我使用 Docker 进行本地开发，理想情况下，我希望在 Pi 上运行 Docker。在 Pi 上运行 Docker 非常简单，你只需像安装其他软件一样从 Linux 包管理器中安装它，唯一的区别是你的基础映像需要是 ARM32 映像，谢天谢地微软[为我们](https://hub.docker.com/_/microsoft-dotnet-core-runtime)提供了这个(作为参考，我使用`mcr.microsoft.com/dotnet/core/runtime:2.2.5-stretch-slim-arm32v7`作为我的基础映像)。

### 把集装箱拿到码头上

我已经有了我的`Dockerfile`，我可以制作一个图像，但是我如何把它放到 Pi 上，然后运行一个容器呢？

**DevOps** ！🎉

我最初的想法是在 Raspberry Pi 上部署一个 Azure Pipeline 代理，然后将它添加到我的 Azure Pipelines 代理池中。我知道这是可能的，我见过达米安·布雷迪(Damian Brady)这样做，但事实证明他使用的是定制编译的代理，这不是真正的支持。所以这并不理想，我不想要一个我必须不断确保有效的解决方案，我只想要有效的东西。

这也是我来到 Azure IoT Edge 的原因。

## 物联网边缘简介

物联网边缘是 [Azure 物联网套件](https://docs.microsoft.com/en-us/azure/iot-fundamentals/?WT.mc_id=devto-blog-aapowell)的一部分，它旨在将可重复使用的模块部署到物联网设备上，也称为“边缘设备”。

物联网边缘的工作方式是在与物联网中枢通信的设备上运行代理。在 IoT Hub 中，您可以针对设备创建部署，在部署中指定想要在设备上运行的模块，即 Docker 映像。物联网边缘检查部署，当它发现一个部署时，它会将其拉下，抓取 Docker 映像并运行容器。[这里的](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime?WT.mc_id=devto-blog-aapowell)是理解物联网边缘架构及其如何融入解决方案的一个很好的起点。我不会详细讨论所有这些，而是将重点放在使用物联网边缘进行部署时您需要了解的几个方面。

## 为开发运维准备好解决方案

如果你像我一样，在了解所有需要使用的东西之前就开始构建一个项目，你可能需要将物联网边缘改造到其中。物联网边缘文档有一个很好的[入门指南](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux?WT.mc_id=devto-blog-aapowell)，如果您刚刚入门，我鼓励您阅读该指南，但由于我在构建物联网边缘之前并不了解它，所以让我们来看看需要做些什么来*物联网边缘化*一个项目。

### Docker 图片

IoT Edge 工作方式的真正好处是，它使用 Docker 作为您想要在设备上运行的应用程序的交付机制，唯一的规定是映像使用 ARM32(或 ARM64，如果您的设备运行的是 arm 32)。所以这意味着我们需要创建一个额外的 docker 文件，以便能够在我们的 Raspberry Pi 上运行。

```
FROM mcr.microsoft.com/dotnet/core/runtime:2.2.5-stretch-slim-arm32v7

WORKDIR /app
COPY ./bin/Release/netcoreapp2.2/publish ./

ENTRYPOINT ["dotnet", "Sunshine.Downloader.dll"] 
```

Enter fullscreen mode Exit fullscreen mode

*[来源于 GitHub 上的](https://github.com/aaronpowell/sunshine/blob/1ecabaa3905198a650ecacc9a1b51811aea4758c/src/Sunshine.Downloader/Dockerfile.arm32v7)*

哦，这与我们如何进行本地开发并没有太大的不同，事实上，它实际上是一个非常基本的 does 文件，它所做的只是复制构建工件(正如我在[本地开发帖子](https://dev.to/azure/home-grown-iot-local-dev-246b)中讨论的，我们没有使用多级 does 文件)。

### 物联网边缘模块

在我们讨论如何利用物联网边缘进行部署之前，我想先谈谈[物联网边缘模块](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-modules?WT.mc_id=devto-blog-aapowell)。模块是在您的设备上运行的应用程序，在 JSON 文件中定义。一个设备上可能部署了多个模块，但是一个模块就是一个应用程序。

```
{  "$schema-version":  "0.0.1",  "description":  "",  "image":  {  "repository":  "${CONTAINER_REGISTRY_SERVER}/sunshine-downloader",  "tag":  {  "version":  "${BUILD_BUILDID}",  "platforms":  {  "amd64":  "./Dockerfile.amd64",  "amd64.debug":  "./Dockerfile.amd64.debug",  "arm32v7":  "./Dockerfile.arm32v7"  }  },  "buildOptions":  [],  "contextPath":  "./"  },  "language":  "csharp"  } 
```

Enter fullscreen mode Exit fullscreen mode

*[来源于 GitHub 上的](https://github.com/aaronpowell/sunshine/blob/1ecabaa3905198a650ecacc9a1b51811aea4758c/src/Sunshine.Downloader/module.json)*

这是我的阳光下载模块的 JSON。这里有一些元数据(`description`，`language`)，其中重要的部分是`image`。在这里，我们定义我们将如何构建我们的图像，以及它将被发布到哪个容器注册中心。

首先，注册表。这可以发布到一个私有注册中心，比如你自己的 [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/?WT.mc_id=devto-blog-aapowell) (这是我用的)，也可以发布到一个公共注册中心，比如 Docker Hub(虽然我不建议这么做)。您需要确保注册表可以被您要部署到的设备访问，因此它们需要在同一个本地网络、vpn 上，或者它需要是互联网可寻址的。

接下来，您为图像定义`tag`，以及将用于创建它们的`Dockerfile`。我正在生成 3 个图像，两个 AMD64 图像(一个包含调试符号)和我的 ARM32 图像。相对于标签所代表的架构来命名标签通常是一种好的做法，但这不是强制性的。我们很快就会需要这些名字，所以要确保它们不会太晦涩难懂。

最后，我们可以给最终将在`buildOptions`中执行的`docker build`命令一些参数。

你可能已经在上面的 JSON 中注意到了，我有一些`${...}` *的东西*。这些是对环境变量的引用，当我创建部署时，这些变量是可用的。`CONTAINER_REGISTRY_SERVER`需要是你正在使用的注册表的 URL，我从 [Azure Pipeline 构建变量](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml&WT.mc_id=devto-blog-aapowell#build-variables)中传递了`BUILD_BUILDID`，但是它可以是你想要用来标记版本的任何东西。

我发现对模块很重要的一点是，你需要命名文件`module.json`并把它放在你正在使用的`Dockerfile`旁边。因为这个原因，我最终把所有的文件都放在了与下载程序源代码相同的文件夹中。

## 物联网边缘部署

定义好我们的模块后，我们可以继续创建物联网边缘部署。为此，我们需要创建一个`deployment.template.json`文件，这是我们的部署模板，将用于为我们不同的模块平台创建部署。

```
{  "$schema-template":  "1.0.0",  "modulesContent":  {  "$edgeAgent":  {  "properties.desired":  {  "schemaVersion":  "1.0",  "runtime":  {  "type":  "docker",  "settings":  {  "minDockerVersion":  "v1.25",  "loggingOptions":  "",  "registryCredentials":  {  "YourACR":  {  "username":  "${CONTAINER_REGISTRY_USERNAME}",  "password":  "${CONTAINER_REGISTRY_PASSWORD}",  "address":  "${CONTAINER_REGISTRY_SERVER}"  }  }  }  },  "systemModules":  {  "edgeAgent":  {  "type":  "docker",  "settings":  {  "image":  "mcr.microsoft.com/azureiotedge-agent:1.0.7",  "createOptions":  ""  }  },  "edgeHub":  {  "type":  "docker",  "status":  "running",  "restartPolicy":  "always",  "settings":  {  "image":  "mcr.microsoft.com/azureiotedge-hub:1.0.7",  "createOptions":  {  "HostConfig":  {  "PortBindings":  {  "5671/tcp":  [{  "HostPort":  "5671"  }],  "8883/tcp ":  [{  "HostPort":  "8883"  }],  "443/tcp":  [{  "HostPort":  "443"  }]  }  }  }  }  }  },  "modules":  {  "SunshineDownloader":  {  "version":  "1.0",  "type":  "docker",  "status":  "running",  "restartPolicy":  "always",  "settings":  {  "image":  "${MODULES.Sunshine.Downloader}",  "createOptions":  {}  }  }  }  }  },  "$edgeHub":  {  "properties.desired":  {  "schemaVersion":  "1.0",  "routes":  {  "route":  "FROM /* INTO $upstream"  },  "storeAndForwardConfiguration":  {  "timeToLiveSecs":  7200  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

*[来源于 GitHub 上的](https://github.com/aaronpowell/sunshine/blob/1ecabaa3905198a650ecacc9a1b51811aea4758c/.build/deployment.template.json)*

这个文件有点大，所以是时候对它进行分解并了解我们需要了解的内容了。

它首先在`$edgeAgent`节点中定义一些关于在我们的设备上运行的代理的信息。您会注意到，这里有您的容器注册中心(ACR 或其他)的凭证，就像`module.json`文件一样，这些凭证来自您在从模板生成部署之前设置的环境变量。

接下来，我们定义`systemModules`，它告诉已部署的边缘代理要运行的边缘代理，这实际上意味着我们可以通过创建部署来配置和升级代理。代理是一个码头工人的形象([在这里找到](https://hub.docker.com/_/microsoft-azureiotedge-agent))作为是边缘枢纽([在这里找到](https://hub.docker.com/_/microsoft-azureiotedge-hub))。我使用的是我构建 Sunshine 时的稳定版本`1.0.7`,但是你可能会得到一个更新的版本。这两个部分都很重要，因为代理是与物联网中心对话、报告状态等的容器。而 Hub 则是你的应用和物联网 Hub 之间的桥梁。

在`modules`节点中，您定义希望安装到您的设备中的模块。这是一个 JSON 对象，你可以定义任意多的对象，如果你在你的设备上做一个`docker ps`(我用的是`SunshineDownloader`)，你给它起的名字就是这个名字，所以它需要符合 Docker 容器命名惯例。您需要设置的模块定义的两个重要部分是 Image 变量和容器需要的任何`createOptions`。

对于`image`属性，我有`${MODULES.Sunshine.Downloader}`，但是它代表什么，它是如何工作的？这个变量由两部分组成，第一部分，`MODULES`是物联网边缘工具寻找的`MODULES_PATH`。默认情况下，它会寻找一个名为`modules`的文件夹，该文件夹相对于模板所在的目录([源引用](https://github.com/Azure/iotedgedev/blob/b7742a1925581ea0db9848ccaa1bdf05ed191f63/iotedgedev/constants.py#L3))，但可以使用`.env`文件覆盖。事实上，[这就是我所做的](https://github.com/aaronpowell/sunshine/blob/1ecabaa3905198a650ecacc9a1b51811aea4758c/.build/.env)，因为我的源代码在`src`文件夹中。变量的其余部分是模块所在的`MODULES_PATH`中的文件夹，直到`module.json`。

我们的 JSON 中的最后一个节点是`$edgeHub`,它是物联网中心模块的一些指令，允许我们控制有关它的信息。我用这个来确保来自我的模块的*消息通过`route`到达物联网中心。定义一条`FROM /* INTO $upstream`的路由基本上就是说“来自设备的每条消息都进入主物联网集线器端点”。这可用于配置消息的预路由，但我改为在物联网集线器内路由消息。*

## 运行部署

我们的部署模板已创建，我们的模块已定义，我们的映像已准备好构建，剩下的就是部署了！

为了进行部署，我们将使用[物联网边缘开发工具](https://aka.ms/iotedgedev)，这是一个用于物联网边缘的开源 Python 应用程序。

### 创建图像

因为我的`deployment.template.json`文件位于`.build`文件夹中(我喜欢将不同任务的文件放在存储库根目录之外)，所以我们将导航到那里。现在我们可以运行`iotedgedev build`，它将为我们建立形象。默认情况下，它将使用`amd64`架构并“推送”到您的本地 Docker 注册表，但我们希望部署到我们的 Pi，因此我们需要编辑`.env`并将`IOTHUB_CONNECTION_STRING`和`DEVICE_CONNECTION_STRING`条目设置到 Azure 中的适当部分(注意:我**没有将这些条目**签入源代码控制中！)并且您需要传递带有`arm32v7`的`--platform`参数来构建模块映像。

构建完成后，您会发现一个名为`deployment.<platform>.json` (so `deployment.arm32v7.json`)的新部署文件，我们将用它来部署到设备上。

### 发布图像

随着我们的映像的构建，我们可以将它推送到我们的容器注册表中，再次支持您需要在之前将`CONTAINER_REGISTRY_USERNAME`、`CONTAINER_REGISTRY_PASSWORD`和`CONTAINER_REGISTRY_SERVER`环境变量设置为 ACR(或您想要推送到的任何其他容器注册表)**，因为它们被添加到了`deployment.<platform>.json`文件中。然后执行`iotedgedev push`命令，将你的镜像推送到注册表中。该命令将需要进行构建，所以如果您想跳过图像创建，请通过`--no-build`。**

### 部署图像

对于我们部署映像，我们不会使用`iotedgedev`工具，而是使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure?WT.mc_id=devto-blog-aapowell) ，特别是[物联网扩展](https://github.com/azure/azure-iot-cli-extension)，因此获取 Azure CLI(我使用它的 Docker 发行版)，登录到您的帐户并安装物联网扩展。

我们将使用上面指定的部署模板，使用 [`edge deployment create`](https://docs.microsoft.com/en-us/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create?WT.mc_id=devto-blog-aapowell) 命令在物联网中心为物联网边缘设备创建部署。

```
$> az iot edge deployment create --deployment-id deployment-01 --hub-name <iot hub name in Azure> --content <path to deployment JSON file> --target-condition deviceId='<name of IoT device in Azure>' --priority 0 
```

Enter fullscreen mode Exit fullscreen mode

假设一切顺利，你现在将看到 Azure 中列出了一个针对物联网边缘设备的部署，不久设备将提取映像并从中启动一个容器！

## 结论

唷，这是一个有点复杂的博客，因为当您开始考虑部署到物联网设备时，会有许多小部件发挥作用。不可否认，我给自己增加了一点难度，因为在决定使用物联网边缘进行部署之前，我选择了创建应用*的路线。如果我要重新开始，我会更多地坚持 docs 网站上的指导大纲，这里有一个很好的分步指南[开始](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux?WT.mc_id=devto-blog-aapowell)整个过程。*

我们首先定义我们的模块，这是我们将在物联网设备上作为 Docker 容器运行的*东西*，接下来我们创建一个`deployment.template.json`文件，这是一个描述如何部署到平台的通用模板，最后我们可以使用 Azure CLI 创建一个部署供我们的设备使用。

下一次，我们将不再自己执行命令，而是通过 Azure 管道来控制一切。