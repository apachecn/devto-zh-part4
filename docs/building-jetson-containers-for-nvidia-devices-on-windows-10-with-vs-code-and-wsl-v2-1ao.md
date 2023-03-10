# 使用 VS 代码和 WSL v2 在 Windows 10 上为 Nvidia 设备构建 jetson-containers

> 原文：<https://dev.to/azure/building-jetson-containers-for-nvidia-devices-on-windows-10-with-vs-code-and-wsl-v2-1ao>

[![](img/310b2b07f52d266b5b2090f07c7a2e07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4BAincnL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/EAqvVhfX4AEThem%3Fformat%3Djpg%26name%3Dlarge)

在这篇文章中，我们将使用 [Visual Studio 代码](https://code.visualstudio.com/Download?WT.mc_id=devto-jetsoncontainers-pdecarlo)和【Linux 的 Windows 子系统版本 2 在 Windows 10 上构建[伊恩·戴维斯的`jetson-containers`项目](https://github.com/idavis/jetson-containers)。简而言之，`jetson-containers`项目允许您构建 CUDA 兼容的映像，以容器的形式运行 GPU 加速的应用程序。该项目支持所有当前的 Nvidia Jetson 设备( [Nano](https://amzn.to/2WFE5zF) 、 [TX2](https://amzn.to/3330jju) 、 [Xavier](https://amzn.to/2XMaSIL) 等)。)并且还支持第三方载板，如用于 Nvidia Jetson TX2/TX2i/TX1 的[orbity 载体。](http://connecttech.com/product/orbitty-carrier-for-nvidia-jetson-tx2-tx1/)

将 Nvidia CUDA 驱动程序和必要的运行时依赖项容器化是令人兴奋的，因为它可以与 container orchestrator 解决方案(如 [Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/about-iot-edge?WT.mc_id=devto-jetsoncontainers-pdecarlo) )相结合，以创建云可配置的物联网部署，这些部署利用了 Nvidia Jetson 设备上的 GPU 加速功能。这为计算机视觉、ML 处理和其他人工智能工作负载开辟了一个可能性领域，这些工作负载现在可以部署到边缘环境中，并使用云定义的配置进行远程更新。在我看来，这将为世界开启一个新的人工智能解决方案范式，这些解决方案将被部署到以前从未考虑过的领域，并由一流的软件生命周期支持提供支持，使用微软 Azure 中可用的[物联网服务。](https://azure.microsoft.com/en-us/overview/iot?WT.mc_id=devto-jetsoncontainers-pdecarlo)

## 第一步:在 Windows 10 上安装 Linux 的 Windows 子系统

在 Windows 10 中，微软发布了一个名为[Windows Subsystem for Linux(WSL)](https://docs.microsoft.com/en-us/windows/wsl/about?WT.mc_id=devto-jetsoncontainers-pdecarlo)的新功能。这个特性允许您在基于 Ubuntu 的环境中直接在 Windows 上运行 bash shell。在这个环境中，您可以交叉编译 AARCH64 的映像，而不需要单独的 Linux VM 或服务器。注意，虽然 WSL 可以和其他 Linux 版本一起安装，比如 OpenSUSE，但是下面的指令只在 Ubuntu 上测试过。

Windows 10 之前的 Windows 版本不支持此功能。此外，它仅适用于 64 位版本的
Windows。

安装 WSL 的完整说明可以在 WSL 的官方微软文档页面上找到。

要在安装了 Fall Creators Update(版本> = 16215.0)的 Windows 10 上安装 WSL，请执行以下操作:

1).启用 Linux 的 Windows 子系统功能

*   打开 Windows 功能对话框(`OptionalFeatures.exe`)
*   启用“Linux 的 Windows 子系统”
*   如有必要，单击“确定”并重启

2).安装 Ubuntu

*   打开微软商店，搜索“Ubuntu 18.04”或使用[此链接](https://www.microsoft.com/store/productId/9N9TNGVNDL3Q)
*   单击安装

3).完整安装

*   打开 cmd 提示符并键入“Ubuntu1804”
*   创建一个新的 UNIX 用户帐户(这是一个独立于您的 Windows 帐户的帐户)

# 第二步:将 Linux 的 Windows 子系统升级到 v2

WSL 2 是该体系结构的一个新版本，它支持 Linux 的 Windows 子系统在 Windows 上运行 ELF64 Linux 二进制文件。它的主要目标是提高文件系统性能，以及增加完整的系统调用兼容性。这种新的架构改变了这些 Linux 二进制文件与 Windows 和计算机硬件的交互方式，但仍然提供了与 WSL 1(当前广泛使用的版本)相同的用户体验。单个 Linux 发行版既可以作为 WSL 1 发行版运行，也可以作为 WSL 2 发行版运行，可以随时升级或降级，并且可以同时运行 WSL 1 和 WSL 2 发行版。WSL 2 使用一种全新的架构，该架构使用真正的 Linux 内核。

支持 WSL v2 需要按照上一节中的说明安装 WSL v1。

升级到 WSL v2 的完整说明可在 WSL v2 的官方微软文档页面上获得。
要在 Windows 10(版本> = 18917.0)上安装 WSL v2，请执行以下操作:

1).启用虚拟机平台功能

*   打开 Windows 功能对话框(`OptionalFeatures.exe`)
*   启用“虚拟机平台”
*   如有必要，单击“确定”并重启

2).将 Ubuntu1804 设置为由 WSL 2 支持

*   打开 Powershell 并运行:

```
 wsl --set-version Ubuntu-18.04 2 
```

3).验证 Ubuntu-18.04 正在使用 WSL v2

*   打开 Powershell 并运行:

```
 wsl --list --verbose 
```

*   验证输出如下所示:

```
 NAME            STATE           VERSION
    * Ubuntu-18.04    Running         2 
```

在确认您正在使用 WSL v2 运行 Ubuntu-18.04 之后，您就可以开始执行下面的“交叉编译”步骤了。

## 步骤 3:使用 Linux v2 的 Windows 子系统在 Ubuntu 上设置交叉编译环境

我们将使用 Ubuntu 18.04 WSL v2 环境来交叉编译能够在 Nvidia Jetson 硬件上运行的 AARCH64 兼容 jetson-containers 映像。

1).将 Nvidia SdkManager 安装到 WSL v2 环境中

*   从您的 Windows 主机操作系统中，获取。来自 developer.nvidia.com 的 Nvidia SdkManager 的 deb 安装程序

    注意:这需要你有一个有效的注册账户@ developer.nvidia.com

*   放置下载的。deb 安装程序到 c:\sdkmanager

*   打开 Ubuntu-18.04 的 WSL v2 兼容实例

*   通过在 WSL v2 实例的 bash 提示符下运行以下命令来安装 SDK manager:

```
 sudo apt update
    sudo apt install -y  libcanberra-gtk-module libgconf-2-4 libgtk-3-0 libxss1 libnss3 xvfb  
    sudo dpkg -i /mnt/c/sdkmanager/sdkmanager_*.deb 
```

2).为 AARCH64 支持安装交叉编译工具

*   通过在 WSL v2 实例的 bash 提示符下运行以下命令，安装交叉编译所需的依赖项:

```
 sudo apt install -y build-essential qemu-user-static binfmt-support 
```

3).安装 Docker

*   通过在 WSL v2 实例的 bash 提示符下运行以下命令，安装 Docker 的 Linux 本地实例:

```
 sudo apt install -y curl
    curl -fsSL https://get.docker.io | bash
    sudo usermod -aG docker $USER 
```

## 步骤 4:使用 Windows 子系统 for Linux 和 Visual Studio 代码构建 Jetson 容器

在使用 Windows Subsystem for Linux v2 在 Ubuntu 上设置了交叉编译环境之后，现在就可以开始构建 jetson-containers 了。

1).配置 Visual Studio 代码

*   将 [Visual Studio 代码](https://code.visualstudio.com/Download?WT.mc_id=devto-jetsoncontainers-pdecarlo)安装到 Windows 主机操作系统上
*   从 Visual Studio 市场安装并启用[远程 WSL 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)

[![](img/d9be487e3e87cd1f612098c69d97a5d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a66tHNUj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khtrm6whfey6rajqnqz0.PNG)

2).启动 docker 和 binfmt-支持服务

*   每次在主机上启动新的 WSL v2 实例时，都必须手动启动这些服务。要启动它们，请在 WSL v2 实例的 bash 提示符下执行以下命令:

```
 sudo service docker start
  sudo service binfmt-support start 
```

注意:如果您希望在启动一个新的 WSL v2 实例时自动启动这些服务，您可能希望将这些行添加到您的`~/.bashrc`文件中

[![](img/37f034d55b967dbeccad2c0813ebd1e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6iqBDkDm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4gulm8a7am7jgcslvsf7.PNG)

3).将 jetson-containers 项目克隆到 WSL v2 环境中，并在 VS 代码中打开

*   通过在 WSL v2 实例的 bash 提示符下运行以下命令，克隆 jetson-containers 项目并在 VS 代码中打开:

```
 git clone https://github.com/idavis/jetson-containers.git
  cd jetson-containers
  code . 
```

4).在 VS 代码中运行构建任务来创建 jetson-containers 映像

*   在上一步中，应该已经打开了一个新的 Visual Studio 代码实例。如果没有，在主机操作系统上打开一个新的 VS 代码实例。复制包含。环境温度至。接下来，在 VS 代码实例内部，按“CTRL+SHIFT+B ”,调出可用构建任务列表，并选择一个任务开始构建相关的 jetson-containers 映像

注意:在构建后续容器之前，需要构建一个<jetpack-depencies>依赖映像。这将要求你有一个来自 https://developer.nvidia.com[的有效账户，并且你已经在。您创建的 env 文件。请注意，在任何任务的构建过程中，在检索 SDK 的过程中，系统会提示您输入帐户密码。](https://developer.nvidia.com)</jetpack-depencies>

[![](img/cf7ffaf7f04af72c1b51e5a76f356020.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wESeAMSF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nzy3ltpj9b7lu8crv0fa.PNG)

## 结论

令人惊讶的是，Linux v2 的 [Windows 子系统能够从 Visual Studio 代码构建具有 CUDA 支持的本机 AARCH64 兼容容器。WSL v2 带来了一个完全兼容的 Linux 子系统，具有 100%的 Linux syscall 兼容性，基本上允许您在本地 Linux 环境中构建任何东西。当与](https://docs.microsoft.com/en-us/windows/wsl/wsl2-index?WT.mc_id=devto-jetsoncontainers-pdecarlo)[远程 WSL 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)结合使用时，我们可以从 Windows 10 执行针对 Linux 子系统的构建，从而实现真正的跨平台开发体验。

同样令人惊讶的是，Ian Davis 已经将每个当前可用的 Jetson 平台策划到他的 [`jetson-containers`项目](https://github.com/idavis/jetson-containers)中，允许我们为完全陌生的架构(ARM64)构建与 Nvidia GPU 加速硬件兼容的容器。由于 WSL v2 可以让我们构建 Linux 可以构建的几乎所有东西，我们现在可以将这种开发 GPU 加速容器解决方案的能力带给 Windows 上使用 VS 代码和 WSL v2 的开发人员。

随着`jetson-containers`开发的进展，您可以在我接下来的文章中看到更多关于如何使用它的细节。要查看 dev.to 上的相关内容，请查看[通过 Nvidia Jetson 设备上的 GPU 加速增强您的容器化物联网工作负载](https://dev.to/azure/supercharge-your-containerized-iot-workloads-with-gpu-acceleration-on-nvidia-jetson-devices-4532)，[在 Nvidia Jetson 设备上开始物联网边缘开发](https://dev.to/azure/getting-started-with-iot-edge-development-on-nvidia-jetson-devices-2dfl/edit)，[在 Nvidia ARM64 设备上开始 DevOps CI / CD 管道](https://dev.to/azure/getting-started-with-devops-ci-cd-pipelines-on-nvidia-arm64-devices-4668)，以及[使用带有 Azure 物联网边缘的认知服务容器](https://dev.to/azure/using-cognitive-services-containers-with-azure-iot-edge-1e5a)

下次见，黑客快乐！