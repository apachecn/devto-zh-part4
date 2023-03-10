# 用 SignalR 和 ChartJS 在浏览器中显示实时传感器数据

> 原文：<https://dev.to/alistairjevans/displaying-real-time-sensor-data-in-the-browser-with-signalr-and-chartjs-2n8c>

在我之前关于改装我的划船机的帖子中，我[在我的划船机](https://dev.to/alistairjevans/modding-my-rowing-machine-with-an-arduino-part-2-reading-the-speed-sensor-b58)上连接了一个 Arduino，并将[速度传感器数据传输到一个 speed 核心应用程序。](https://dev.to/alistairjevans/streaming-real-time-sensor-data-to-an-asp-net-core-app-from-an-arduino-3ilo)

在本文中，我将向您展示如何获取传感器和计数器数据，在数据到达时将其推送到浏览器，并在实时图表中显示出来。

> 如果你想跳过前面，我已经上传了 Arduino 和 ASP.NET 组件的所有代码到 https://github.com/alistairjevans/rower-mod 的 github repo。
> 
> 我使用的是 Visual Studio 2019 和 ASP.NET 核心 3.0 预览版，用于所有服务器端组件，但 ASP.NET 核心的最新稳定版本将工作得很好，我没有使用任何新功能。

### 将数据推送到浏览器

所以，你可能听说过 [SignalR](https://docs.microsoft.com/en-us/aspnet/core/signalr/introduction?view=aspnetcore-3.0) ，这种 ASP.NET 技术可以用来将数据从服务器推送到浏览器，通常会在两者之间建立更紧密的关系。

我将使用它在新的传感器数据到达时向浏览器发送数据，并让浏览器请求重置计数。

整体组件布局如下所示:

[![](img/12cf2538192ef31568d25dc5e456268d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g6k-BFt4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alistairevans.files.wordpress.com/2019/06/image.png%3Fw%3D536%26h%3D331)

### 设置信号员

这一点很简单；首先，转到您的 ASP.NET 应用程序项目中的 Startup.cs 文件，在`ConfigureServices`方法中，添加 SignalR: