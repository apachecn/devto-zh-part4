# 如何更改默认的 DotNet SDK 版本

> 原文：<https://dev.to/polarbit/how-to-change-default-dotnet-sdk-version-43ph>

我装的时候”。Net Core 3 SDK (preview 6)”，我系统上默认版本的 *dotnet core sdk* 改成这个新版本。然后我需要在某些地方将默认版本恢复为“2.x.x”。于是我按照下面的步骤。

参见[官方文件](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)

#### 查看已安装的 DotNet 核心 SDK 和运行时版本

为了查看所有安装的 **dotnet SDK** 版本(以及目前使用的默认版本)，运行:
`dotnet --version`
`dotnet --list-sdks`
[![](img/8e09ad91daa5877af90c6f238814478a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EBMs7lt3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x392pgbd9wpg0g6dim84.PNG)

为了查看所有安装了**的 dotnet 运行时的**版本，运行:
`dotnet --list-runtimes`
[![](img/6c134c92642dab78a52963b45b989b1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YIDSoOOu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q7wjub3ggdmgu9bitayl.PNG)

#### 使用 global.json 文件更改默认的 DotNet SDK 版本

为了更改默认的 *dotnet SDK 版本*，将一个 global.json 文件放在当前文件夹或任何父文件夹中，其内容如下。您可以手动创建 global.json 文件，或者使用 dotnet cli 命令`dotnet new globaljson`。