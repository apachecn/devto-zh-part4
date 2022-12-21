# 在点网络核心中启用 CORS

> 原文：<https://dev.to/0xshetty/enabling-cors-in-dotnet-core-29fg>

[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) -跨来源的资源共享，基本上帮助我们的应用免受跨站点脚本攻击。这将限制网站访问或发送数据到另一个来源(web 应用程序)。

这篇文章将有助于研究在 dotnet 核心应用程序中启用 CORS。在发布实际请求之前，每个 POST 请求都会使用 OPTION 方法发送一个[pre flinded requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Preflighted_requests)，如下例所示。

```
 OPTIONS {{apiendpoint}}/api/web/incoming HTTP/1.1
            Access-Control-Request-Method: POST
            Origin: http://localhost:4200 
```

Enter fullscreen mode Exit fullscreen mode

这是为了检查在这种情况下 POST 的实际请求方法是否被允许。

因此，为了在 dotnet core 中实现这一点，我们在下面的`StartUp.cs`中使用 IServiceCollection 的 [AddCors](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.mvccorsmvccorebuilderextensions.addcors?view=aspnetcore-2.2) 。

```
 services.AddCors(options =>
            {
                options.AddPolicy("CorsPolicy",
                    builder => builder.AllowAnyOrigin()
                    .AllowAnyMethod()
                    .AllowAnyHeader()
                    .AllowCredentials());
            }); 
```

Enter fullscreen mode Exit fullscreen mode

我们已经根据我们的要求创建了策略，现在需要在我们的 MVC 应用程序中使用它，请确保在`UseMvc`之前添加`UseCors`。

```
 app.UseCors("CorsPolicy");
            app.UseMvc(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的应用程序 CORS 已启用。这种方法是全局添加 CorsPolicy。您还可以参考本[指南](https://docs.microsoft.com/en-us/aspnet/core/security/cors?view=aspnetcore-2.2#enable-cors-with-attributes)来使用属性启用 CORS。

*   照片由 Unsplash 上的谐趣快照提供

*   最初发布于 [BitsMonkey](https://unrealnerd.github.io/blog/2019/07/07/cors-dotnetcore-webapi)