# ASP.NET 核心集成测试简介

> 原文：<https://dev.to/kaos/introduction-to-asp-net-core-integration-testing-1e77>

在这篇文章中，我将向你简要介绍如何测试你的 ASP.NET 核心控制器。

请注意，术语“集成测试”还包括其他情况，例如测试数据访问或访问文件系统。

## 设置项目

一个常见的指导方针是以如下方式构建您的 ASP.NET 核心项目:

```
.
├── src
│   └── MyProject.Api
│       └── Controllers
│           └── ValuesController
└── test
    ├── IntegrationTests
    │   └── MyProject.Api.Test
    │       └── ValuesControllerTest
    └── UnitTests 
```

## 安装测试主机 NuGet

创建一个. NET 核心测试项目，然后安装`Microsoft.AspNetCore.TestHost`包。
这个包将提供配置`TestServer`的选项。

`> Install-Package Microsoft.AspNetCore.TestHost`

## 创建基类

创建一个我们的`ControllerTest`类可以继承的基类。我们将设置用于执行请求的`TestServer`和`HttpClient`。

```
[TestClass]
public abstract class IntegrationTestInitializer
{
    protected HttpClient _client;

    [TestInitialize]
    public void Setup()
    {
        var builder = new WebHostBuilder()
            .UseStartup<Startup>();
        var server = new TestServer(builder);

        _client = server.CreateClient();
    }
} 
```

## 创造一个`ControllerTest`

让我们假设我们想要测试的控制器看起来像这样

```
[Route("api/[controller]")]
[ApiController]
public class ValuesController : ControllerBase
{
    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        return new string[] { "value1", "value2" };
    }
} 
```

设置好基类后，我们就可以简单地从基类继承并使用我们的`_client`来执行针对 API 的请求。

```
[TestClass]
public class ValuesControllerTest : IntegrationTestInitializer
{
    [TestMethod]
    public async Task CanGetValues()
    {
        List<string> expectedResponse = new List<string> { "value1", "value2" };

        var responseJson = await _client.GetStringAsync("api/values");
        List<string> actualResponse = JsonConvert.DeserializeObject<List<string>>(responseJson);

        Assert.AreEqual(expectedResponse.Count, actualResponse.Count);
        foreach(var expectedValue in expectedResponse)
        {
            Assert.IsTrue(actualResponse.Contains(expectedValue));
        }
    }
} 
```

这篇文章描述了如何集成测试一个非常基本的 ASP.NET 核心 API。

在现实世界中，您可能会在 API 后面有一个必须被访问的数据库，一些端点也可能受到 API 密钥或 JWT 的保护。然而，这篇文章只是作为集成测试的一个快速介绍。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [凯-奥斯瓦尔德](https://github.com/kai-oswald)/[IntegrationTestSample](https://github.com/kai-oswald/IntegrationTestSample)

### 展示如何编写集成测试的 ASP.NET 核心示例项目

<article class="markdown-body entry-content p-5" itemprop="text">

# 集成测试示例

展示如何针对控制器进行集成测试的 ASP.NET 核心示例项目

</article>

[View on GitHub](https://github.com/kai-oswald/IntegrationTestSample)