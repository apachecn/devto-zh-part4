# 用 swash buckle-Making Space potatos V-x . x . x 对 Space 核心 API 进行版本控制

> 原文：<https://dev.to/htissink/versioning-asp-net-core-apis-with-swashbuckle-making-space-potatoes-v-x-x-x-3po7>

[更新路径版本发布在这里](https://dev.to/htissink/asp-net-core-api-path-versioning-197o)

用 ASP.NET 核心创建一个新的 API 既有趣又容易；对 API 进行版本控制有点困难。不过，为初始版本生成 swagger 是一件轻而易举的事情，而生成版本化的 Swagger 则有点棘手。

**免责声明:由于 ASP.Net 核心 2.2.0 的 bug，请使用 ASP.Net 核心 2.1.0**

让我们通过创建一个简单的 Space 核心 Api (2.1.0)来深入研究这个问题，给我们一些*太空土豆*。首先我们将创建一个名为`SpaceFarmController`的控制器。

```
[Route("api/[controller]")]
[ApiController]
public class SpaceFarmController : ControllerBase
{
    [HttpGet("Potatoes")]
    public string SpacePotatoes() => "Space Potatoes v1";
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们给这个*小* API 添加一些版本控制。将以下 Nuget 包添加到您的项目中:

*   **微软。AspNetCore.Mvc.Versioning(版本 3.0.0)**

我们的 API 将使用*路由版本控制*进行版本控制，这仅仅意味着我们的 API 将根据其路由进行版本控制

| 版本 | 途径 |
| --- | --- |
| 第五颅神经的眼支 | `.../api/controller/v1/something` |
| V2 | `.../api/controller/v2/something` |
| v{n} | `.../api/controller/v{n}/something` |

现在可以通过向控制器添加属性来轻松地对 API 进行版本控制:

```
[ApiVersion("1")]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiController]
public class SpaceFarmController : ControllerBase
{
    [HttpGet("Potatoes")]
    public string SpacePotatoes() => "Space Potatoes v1";
} 
```

Enter fullscreen mode Exit fullscreen mode

修改`Startup.cs`文件:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddApiVersioning(); // just add this
} 
```

Enter fullscreen mode Exit fullscreen mode

在本地运行你的应用程序，你现在可以通过向`https://localhost:5001/api/v1/spacefarm/potatoes`发出 get 请求来获得一些*太空土豆*。

让我们加入一些狂妄的一代。为此，我们将使用 **Swashbuckle** 。将以下 Nuget 包添加到您的项目中:

*   **Swashbuckle。AspNetCore(版本 4.0.1)**

现在，将 Swagger generation 添加到您的项目中就像将以下内容添加到您的`Startup.cs`文件中一样简单:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddApiVersioning();

    // Add this
    services.AddSwaggerGen(options => 
        options.SwaggerDoc("v1", new Info
        {
            Version = "v1",
            Title = "v1 API",
            Description = "v1 API Description",
            TermsOfService = "Terms of Service v1"
        }));
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    loggerFactory.AddConsole(Configuration.GetSection("Logging"));
    loggerFactory.AddDebug();

    // And add this, an endpoint for our swagger doc 
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint($"/swagger/v1/swagger.json", $"v1");
    });
    app.UseMvc();
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，将应用程序的`launchSettings.json`条目改为:

```
"*YOUR APPLICATION NAME HERE*": {
      "commandName": "Project",
      "launchBrowser": true,
      "launchUrl": "swagger",
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      } 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您运行应用程序时，它会立即启动 Swagger 页面。

如果您正确完成了所有这些操作，当您运行应用程序时，您应该会看到类似于以下内容的屏幕:

[![Alt Text](img/b97249cd7588e8d4338953dcdcc98d69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TmZf0mH---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2tesbv8l6hxethh5f0iw.PNG)

这太棒了——你有了 Swagger generation 和你的 API 的 v1。但是太空土豆市场是一个不断变化的世界，很快你就会想创造一种额外的方式来获得 T2 太空土豆。

现在，添加另一个版本很容易，但是让自大的一代使用这个新版本有点棘手。

首先，让我们在`SpaceFarmController.cs` :
中为我们的端点添加一个 **v2**

```
[Route("api/v{version:apiVersion}/[controller]")]
// Which versions the controller responds to
[ApiVersion("1")]
[ApiVersion("2")]
[ApiController]
public class SpaceFarmController : ControllerBase
{
    [HttpGet]
    // Which version the route corresponds to
    [MapToApiVersion("1")]
    [Route("Potatoes")]
    public string GetPotatoes() => "Space Potatoes v1";

    [HttpGet]
    // Which version the route corresponds to
    [MapToApiVersion("2")]
    [Route("Potatoes")]
    public string GetPotatoesV2() => "Space Potatoes v2";
} 
```

Enter fullscreen mode Exit fullscreen mode

棒极了，*妙极了*甚至。我们现在有两个版本的 API。如果我们运行这个，我们可以向`https://localhost:5001/api/v1/spacefarm/potatoes`和`https://localhost:5001/api/v2/spacefarm/potatoes`发出请求(但是 Swagger 生成会失败)。

Swashbuckle 不知道这两条路线有区别——它认为它们是同一条路线。所以让我们帮助 Swashbuckle。为此，我们需要创建两个类:

```
public class RemoveVersionFromParameter : IOperationFilter
{
    public void Apply(Operation operation, OperationFilterContext context)
    {
        var versionParameter = operation.Parameters.Single(p => p.Name == "version");
        operation.Parameters.Remove(versionParameter);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

和

```
public class ReplaceVersionWithExactValueInPath : IDocumentFilter
{
    public void Apply(SwaggerDocument swaggerDoc, DocumentFilterContext context)
    {
        swaggerDoc.Paths = swaggerDoc.Paths
            .ToDictionary(
                path => path.Key.Replace("v{version}", swaggerDoc.Info.Version),
                path => path.Value
            );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个类都有一种特殊的*过滤器*，Swashbuckle 提供这种过滤器来帮助 Swagger 生成。`RemoveVersionFromParameter`将从 Swagger 文档中删除作为参数的 API 版本。`ReplaceVersionWithExactValueInPath`将路径从可变的`api/v{version:apiVersion}/[controller]`改变为具有固定路径，例如`api/v1/[controller]`。

最后，过滤器将需要应用于`Startup.cs`中的 Swagger 生成，并且需要做一些其他的小修改:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddApiVersioning();

    services.AddApiVersioning(o =>
    {
        o.AssumeDefaultVersionWhenUnspecified = true;
        o.DefaultApiVersion = new ApiVersion(1, 0);
    });

    services.AddSwaggerGen(options =>
    {
        options.SwaggerDoc("v1",
            new Info
            {
                Version = "v1",
                Title = "v1 API",
                Description = "v1 API Description",
                TermsOfService = "Terms of usage v1"
            });

        // Add a SwaggerDoc for v2 
        options.SwaggerDoc("v2",
            new Info
            {
                Version = "v2",
                Title = "v2 API",
                Description = "v2 API Description",
                TermsOfService = "Terms of usage v3"
            });

                // Apply the filters
        options.OperationFilter<RemoveVersionFromParameter>();
        options.DocumentFilter<ReplaceVersionWithExactValueInPath>();

                // Ensure the routes are added to the right Swagger doc
        options.DocInclusionPredicate((version, desc) =>
        {
            var versions = desc.ControllerAttributes()
                .OfType<ApiVersionAttribute>()
                .SelectMany(attr => attr.Versions);

            var maps = desc.ActionAttributes()
                .OfType<MapToApiVersionAttribute>()
                .SelectMany(attr => attr.Versions)
                .ToArray();

            return versions.Any(v => $"v{v.ToString()}" == version)
                          && (!maps.Any() || maps.Any(v => $"v{v.ToString()}" == version));;
        });

    });

}

public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    loggerFactory.AddConsole(Configuration.GetSection("Logging"));
    loggerFactory.AddDebug();

    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint($"/swagger/v1/swagger.json", $"v1");
                // Specify and endpoint for v2
        c.SwaggerEndpoint($"/swagger/v2/swagger.json", $"v2");
    });
    app.UseMvc();
} 
```

Enter fullscreen mode Exit fullscreen mode

所做的大部分事情都是不言自明的。唯一可能感兴趣的部分是`options.DocInclusionPredicate`。定义这个谓词的一小段代码找到了控制器的版本和与特定端点相关联的版本映射，并确保端点包含在正确的 Swagger 文档中。

如果一切都已正确完成，运行应用程序现在应该会启动一个具有多个版本的 Swagger doc，

[![Alt Text](img/c725eda14851fe652c666dc300ab61e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--04asBNsG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0e601fwh4gi4b5cspxzy.png)

和每个版本的路线:

[![Alt Text](img/31c21f80511f40342b65bf8870806a0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8z6Jb_Vn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x4dj9x9atc0frem5k4vn.png)
[![Alt Text](img/83171a21ccc3361d8c103379b9e391d5.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--inzd-TB---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cl48ginjm2hwfvdu3pp8.png)