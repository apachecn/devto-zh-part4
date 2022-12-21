# 构建微服务，规模重要吗？

> 原文：<https://dev.to/dotnet/building-microservices-does-size-matter-2l88>

几个月前，我有机会坐下来与 ASP.NET 团队的格伦·康德伦和瑞安·诺瓦克谈论即将到来的新事物。微服务的 NET 3.0。

[https://www.youtube.com/embed/cNdPbTB72bw](https://www.youtube.com/embed/cNdPbTB72bw)

**主题**

*   [00:22](https://www.youtube.com/watch?v=cNdPbTB72bw&t=22s) -微服务模板的目标是什么。网芯 3.0？
*   [03:05](https://www.youtube.com/watch?v=cNdPbTB72bw&t=185s) -有哪些新的工人模板？
*   [06:57](https://www.youtube.com/watch?v=cNdPbTB72bw&t=417s)-gRPC 正在发生什么？网芯 3？
*   [11:47](https://www.youtube.com/watch?v=cNdPbTB72bw&t=707s) -我们开发人员如何在 gRPC 和 Web APIs 之间做出选择？

**资源**

*   [ASP.NET 核心上的 gRPC 简介](https://docs.microsoft.com/aspnet/core/grpc/?view=aspnetcore-3.0&WT.mc_id=devto-blog-shboyer)
*   [下载。网芯 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0?WT.mc_id=devto-blog-shboyer)
*   [。NET 微服务架构指南](https://dotnet.microsoft.com/learn/web/microservices-architecture?WT.mc_id=devto-blog-shboyer)
*   [比较 gRPC 系列与 HTTP API](https://docs.microsoft.com/aspnet/core/grpc/comparison?view=aspnetcore-3.0&WT.mc_id=devto-blog-shboyer)

# 微服务尺寸重要吗？

这让我更多地思考微服务以及如何看待它们。“微”是这里正确的前缀吗？它们实际上很小吗？大概不会。

这个术语更多的是关于责任、部署(而不是规模)、可管理性、服务边界和开发团队。

# 你好世界

不是收回，而是什么是最低限度的现实服务。我们可以简单地编写一个" Hello World "吗？

```
 public class Service
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Service>();
            });

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapGet("/", async context =>
            {
                await context.Response.WriteAsync("Hello, world!");
            });
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里创建了一个主机，设置了 EndpointRouting 来处理“/”路由并返回“Hello，world！”。

使用`dotnet run`你可以浏览到 [http://localhost:5000](http://localhost:5000) 并看到 **Hello，world！**输出到屏幕上。不太实际，但是，一个负责任的服务做这项工作。

> 使用。NET CLI 创建一个新的 API 模板，使用`dotnet new webapi`来创建一个更完整的模板。参见[教程/文档在这里](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api?view=aspnetcore-2.2&tabs=visual-studio-code&WT.mc_id=devto-blog-shboyer)。

# 专注于工作

构建服务应该更多地是关于创建“小型集中”服务的愿望，而不仅仅是一个小型服务。

这里有一个风险与回报的对话。希望创建尽可能多的小服务，以遵守微服务方法的原则，这将导致一定程度的不值得的开销。

[![](img/7e06b462062046a768906697aaf0cbb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WP9ezcRj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yno5ri8kn8miifby0405.jpg)

不知道你怎么样，但每次我踩到那堆东西都会痛...

# 有界语境

取决于你在哪里看、听、读等。领域驱动的设计可能会对如何在大型系统中整合复杂性产生影响，它有许多很好的工具。

DDD 使用的一个术语是 [aggregate](http://martinfowler.com/bliki/DDD_Aggregate.html) 来描述被视为一个单元的一群或一组对象。因此，在我们处理客户订单的应用程序中，所有“CustomerOrder”操作和数据由单一服务处理是有意义的。

然而，其他“客户”或“订单”操作很容易渗透到服务中。发现自己在分组类或实体，而不是服务的实际功能。

能够独立地维护和部署每个服务/模式是一个很大的好处，并且根据需要扩展每个服务也适用于这种架构。然而，成为网络健谈者是一个副作用。你能接受一堆调用吗？只要它们超快，以换取部署的便利性、可伸缩性和可维护性。

# 多小才算小？

团队小吗？根据应用程序的不同，6-8 个工程师或少数几个工程师似乎是一个理想的数字，但是大型系统是由较小的团队构建的，反之亦然。

我的代码用行数来衡量吗？

磁盘上的大小？有人会说磁盘空间很便宜。

容器每天都在变小，直到你把一个应用程序放进去。

你有什么想法？