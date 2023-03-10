# 如何用 C#和？网

> 原文：<https://dev.to/azure/how-you-can-create-a-serverless-api-in-c-and-net-1ie>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 在这篇文章中，我们将通过使用 C#和. NET 来构建一个无服务器的函数。我们将解释无服务器的原因，还将学习用 VS 代码来构建、运行和调试我们的第一个函数。

如果你对如何用 JavaScript 编写函数感兴趣，看看下面，否则，请继续读下去。

如果您对 JavaScript 和 Serverless 感兴趣，可以看看这个系列

> [https://dev . to/azure/server less-how-you-can-learn-server less-authoring-functions-in-portal-vs-code-write-API-and-more-1cno](https://dev.to/azure/serverless-how-you-can-learn-serverless-authoring-functions-in-portal-vs-code-write-apis-and-more-1cno)

在我们开始构建我们的第一个函数之前，让我们先提一下我们将在本文中涉及的内容:

*   **为什么无服务器**，问自己为什么做你正在做的事情是至关重要的。有一些标准使得使用无服务器功能成为一个好的选择。
*   **核心概念**，无服务器由一组概念组成，您需要了解这些概念才能有效地使用它，并大致了解正在发生的事情。您需要了解的最重要的概念叫做输入/输出绑定。
*   **构建我们的第一个函数**，这里我们将介绍如何通过设置触发器来创建我们的函数，解析来自路由/查询参数或发布主体的输入。我们还将了解如何定义我们的输出。

## 资源

*   注册一个免费的 Azure 账户
    要创建无服务器的 Azure 功能，你需要一个免费的 Azure 账户

*   在 VSCode
    中创建你的第一个 Azure 函数这是第一篇很棒的博客文章，将向你展示函数的创建和部署

*   [使用 C#实现 Azure 功能](https://docs.microsoft.com/en-us/azure/azure-functions/functions-dotnet-class-library?wt.mc_id=devto-blog-chnoring)
    一个 Azure 功能的 C#参考

*   安装 Azure Functions 核心工具
    你需要 Azure Functions 核心工具来运行和调试 VS 代码中的 Azure 功能

*   [Azure Functions 文档概述](https://docs.microsoft.com/en-us/azure/azure-functions/?wt.mc_id=devto-blog-chnoring)
    您还可以使用 Azure CLI 和门户网站等创建 Azure 函数

*   Azure CLI 安装
    Azure CLI 是一种从终端管理云资源的神奇方式，通常比使用门户网站要快得多。

*   [https://docs . Microsoft . com/en-us/azure/azure-functions/functions-triggers-bindings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?wt.mc_id=devto-blog-chnoring)
    所有触发器、输入和输出绑定的完整列表

## 为什么无服务器

无服务器是一种云计算执行模式。

> 好吧，听起来很棘手。我们是什么意思？

简单，我们写的函数会在云端执行。这意味着我们不必关心分配一个虚拟机，甚至不必运行一个 web 服务器来运行代码，这是为我们管理的。这也意味着资源分配是为我们管理的。

> 听起来不错，还有什么？

选择无服务器的主要原因之一是成本或缺乏成本。无服务器很便宜。您通常只在您的功能实际运行时付费。

> 怎么会便宜？

你的函数实际上并不总是在那里，它们的实例会在你需要的时候出现。这意味着你可能会经历所谓的冷启动。这意味着第一次启动一个实例可能需要一些时间，然后您就可以开始与它进行交互了。

> 我不确定这听起来有多好，如果我想让我的客户 24-7 都能达到我的功能，或者至少让它避免这种*冷启动*，那该怎么办？

如果你真的想避免冷启动，有一个额外的计划

> [https://docs . Microsoft . com/en-us/azure/azure-functions/functions-premium-plan](https://docs.microsoft.com/en-us/azure/azure-functions/functions-premium-plan?devto-blog-chnoring)

但是在你开始之前，让我们讨论一下实际上应该运行什么代码。

> 好吗？

是的，所有的代码和业务逻辑都不应该在那里运行。只有你很少做的事情才应该作为无服务器运行，比如一个实用函数或者某种计算。

> 好吧，我明白了，我想我的代码库中有一些代码，我可以把它们转换成不需要经常运行的无服务器代码。我这样做可以省钱，对吗？

是的，没错，很少运行的代码可以变成无服务器的，这样你就可以省下一大笔钱。

> 就这些吗？

嗯，还有更多，无服务器通常是更大图景的一部分，因为它可以与大多数云资源交互。它可以被任何东西触发，从数据库、队列到 HTTP，我们还可以轻松地将计算结果传递给云中的另一个服务

> 哇，听起来...真的很棒。

## 核心概念

好了，我们已经提到了一些核心概念，但让我们更清楚地了解一下它们是什么。

我们得到了以下概念:

*   **触发**，这就是触发我们函数启动的东西。这可能是一个 HTTP 调用、一个引发的事件或数据库中输入的一行等等
*   **输入绑定**，输入绑定是到资源的连接，可以是 SignalR、表存储、CosmosDB 等等。关键是我们不需要实例化一个连接，它是预先创建好的，可以让我们与之交互
*   **输出绑定**，这就是你可以写的东西。例如，您可以使用一个输入绑定来连接到一个数据库，并读取产品列表。相反，在给定函数输入数据的情况下，您可以使用输出绑定来创建新产品条目

这听起来可能有点奇怪，但随着我们学习创建一些函数，这一点会变得更加清晰。

我推荐看看这个链接

> [https://docs . Microsoft . com/en-us/azure/azure-functions/functions-triggers-bindings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?devto-blog-chnoring)

查看所有支持的输入/输出绑定的完整范围

## 构建我们的第一个函数

好了，我们已经到了退出部分，编码一些功能。在我们开始之前，我们将需要安装一些扩展，使我们的创作体验更好一点。

### 设置

我们将需要以下内容:

*   [Azure 功能核心工具](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local#v2?wt.mc_id=devto-blog-chnoring)
*   VS 代码的 Azure Functions 扩展，搜索`Azure Functions`。它应该是这样的:

[![](img/1ccd31316df9b0411ec32c5d323df366.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oBlwwU6Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oi2oldbp394akv3rbhro.png)

*   因为我们将使用 C#我们将需要它的扩展，看起来应该是这样

[![](img/e39a81ad2d71ac54d9e2381cd69d6f6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMPWcw9x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ru0ndx0fdfxid143vp5p.png)

### 咱们码吧

我们准备编码了。我们将执行以下操作:

1.  生成一个 Azure 函数 App
2.  创建一个 HTTP 触发的 Azure 函数

**生成一个 Azure 功能应用**
我们的功能将需要托管在一个应用程序内部。但是一个应用程序可以有几个功能。这非常简单，只需通过选择`View/Command Palette`或点击组合键`COMMAND + SHIFT + P`在 VS 代码中调出命令面板(如果你在 Mac 上)。一旦你有了菜单，输入:`Azure Functions: Create new project`。

这将要求您:

*   **目录**，你的 app 存放在哪个目录，选择你当前的目录
*   **代码语言**，它会继续问你代码语言，选择 C#
*   **触发器**，然后它会要求你进行你的第一个函数触发器，选择 HTTP 触发器
*   **函数名**，之后会询问函数名，输入`Hello`
*   **名称空间**，然后你会被要求一个名称空间，现在，去与`Company.Function`，你可以随时改变它
*   **访问权限**，当询问访问权限时，选择`Anonymous`。安全性是一个大问题，但在这个练习中，我们将重点了解各个部分，我们可以在以后加强安全性。

它还会询问您第一个函数的名称、语言和触发器类型。

这将为您生成一个项目。然而，一开始它会用红色字母抱怨有未解决的依赖关系。您可以通过在项目生成完成后立即弹出的菜单中点击`restore`来解决这个问题，或者您可以在终端中的项目根目录下输入以下命令:

> 点网络恢复

这将恢复项目中指出的依赖关系，您将很快看到代码看起来又变得令人满意了。

**功能剖析**

让我们来看看创建应用程序时生成的文件`Hello.cs`:

```
// Hello.cs

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace Company.Function
{
    public static class Hello
    {
        [FunctionName("Hello")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            dynamic data = JsonConvert.DeserializeObject(requestBody);
            name = name ?? data?.name;

            return name != null
                ? (ActionResult)new OkObjectResult($"Hello, {name}")
                : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
        }
    }
} 
```

我们需要知道的关于 C#中 Azure 函数的大部分事情都在这段代码中。让我们从头开始:

*   **FunctionName** ，这是一个接受字符串参数的装饰器。当我们调试或以其他方式测试我们的应用程序时，这个函数将被调用。字符串参数是`Hello`，所以我们可以预期这个函数的最终 URL 是`[someUrl]/Hello`。
*   **运行**，这是一个任意的函数名，它可以被称为任何东西
*   **HttpTrigger** ，这是一个装饰器装饰我们的 HttpRequest 参数`req`。我们从装饰器开始，将`AuthorizationLevel`设置为`Anonymous`，这意味着外部的任何人都可以访问这个函数。在`AuthorizationLevel`上还有其他选项，我们将在以后的文章中探讨
*   **HTTP 动词列表**，接下来是由 HTTP 动词组成的参数字符串列表。我们可以看到，到目前为止我们已经有了值`get`和`post`，这意味着函数将响应这些 HTTP 动词。我们当然可以扩展列表说`"get", "post", "put", "delete"`，但是在 REST 中，你倾向于每种类型的动作只支持一个动词。
*   **路线模式**，下一个值是`Route`。如果我们改变这一点，我们可以开始支持路由参数，我们将在稍后展示这一点。

这就是 HttpRequest 参数。我们来看看`ILogger`类型的`log`。通过调用这个函数，我们能够写入日志。调试时可以在本地看到日志，部署应用程序后，也可以在门户中看到日志。

好了，让我们看看生成的函数体:

```
string name = req.Query["name"];

string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
dynamic data = JsonConvert.DeserializeObject(requestBody);
name = name ?? data?.name;

return name != null
    ? (ActionResult)new OkObjectResult($"Hello, {name}")
    : new BadRequestObjectResult("Please pass a name on the query string or in the request body"); 
```

第一行告诉我们如何解析出查询参数。我们使用带有字符串参数的`Query`来实现这一点。例如，如果你的 URL 看起来像这样`url/Hello?name='some value'`，变量`name`将保存值`some value`。

接下来，我们使用 StreamReader 读取主体，注意关键字`await`的使用。请注意，这是如何在函数头的开头与一个`async`关键字匹配的。接下来的一行是调用`JsonConvert.DeserializeObject`并将其分配给一个`dynamic`。在下一行，我们试着从我们的主体中读出`name`，假设主体是非空的。最后，我们用`OkObjectResult`返回一个`200`响应，或者用`BadRequestObjectResult`返回一个`401`。

**运行功能**

接下来，我们将使用 VS 代码调试运行该函数，并尝试 GET 和 POST。进入菜单并选择`Debug /Start Debugging`这将编译你的应用程序并启动运行时。完成后，您应该会看到以下内容:

[![](img/2f332d0cb18a7f520d400a4c3611c7e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wwmNygAn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/85yrehv53fp2irk3g3mb.png)

转到您的浏览器并输入指示的 URL `http://localhost:7071/api/hello`:

[![](img/d694d53baa7ba194f5400f05da23dabf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pV8uUrb8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2yo4hwka9bp64n8m8un0.png)

代码显然对我们不满意，因为我们没有提供查询参数或提交主体。让我们用一个查询参数`?name=chris`来试试，我们输入`http://localhost:7071/api/hello?name=chris`，它现在说:

[![](img/23d13b61ddb81d4a356a415f4ebb38e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S9yu5ajm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kcyk92hwami7gp2p1c7l.png)

一切都好！

让我们设置一个断点，然后重新加载浏览器:

[![](img/5d659075bd49fbc22348d2b0ef98d213.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SjQuq2J_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/osvn88l598fn70djv00c.png)

正如你在上面看到的，断点被命中，我们能够像往常一样检查和调试我们的代码，好东西:)

很明显，我们在工作中接到了 get 电话，那么 POST 呢？调出您最喜欢的客户端进行 Rest 调用，无论是 cURL 还是高级 REST 客户端，都由您决定。然后创建一个请求，如下所示:

[![](img/2c492226cb21b8cfc124ca7846dd32d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9SES8sWe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ghap020k6a9693hpvezz.png)

然后查看断点是如何被命中的，就像这样:

[![](img/06944187f9eba4216b09e42c6f21a919.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4d26sQgj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wkeaoqcuszn26bm7fqx4.png)

## 构建一个 REST API

我们想做的不仅仅是一个简单的函数，即生成一个 REST API。我们不会为此使用数据库，而是使用一个静态类来保存状态。

为此创建一个全新的目录，并创建一个新的 Azure 函数应用程序。当询问方法名称时，选择`ProductsGet`和`HttpTrigger`

然后使用命令选项板创建以下方法:

*   产品创建
*   产品获取

为了生成这些方法，我们调出命令面板，这次我们选择`Azure Functions: Create Function`。选择`HttpTrigger`并命名。此时，您的项目应该如下所示:

[![](img/698ac38823e1212b74e91405594655c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yQi5wIKJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aw8kswrt29kx99fp802n.png)

现在怎么办？

我们将按顺序检查每个生成的类，但首先让我们创建保存我们的数据库的类`Db.cs`

### 创建数据库

这只是一个保存数据的简单类，但是在以后的文章中，我们将用一个实际的数据库来替换它。

```
// Db.cs

using System;
using System.Collections.Generic;
using System.Linq;

namespace Company {
  public class Product
  {
    public int Id { get; set; }
    public string Name { get; set; }
  }
  public static class Db {

    private static List<Product> products = new List<Product>(){
        new Product(){ Id = 1, Name= "Avengers End Game" },
        new Product(){ Id = 2, Name= "Wonder Woman" }
    };

    public static IEnumerable<Product> GetProducts() 
    {
      return products.AsEnumerable();
    }

    public static Product GetProductById(int id)
    {
      return products.Find(p => p.Id == id);
    }

    public static Product CreateProduct(string name)
    {
      var newProduct = new Product(){ Id = products.Count + 1, Name = name}; 
      products.Add(newProduct);
      return newProduct;
    }
  } 
} 
```

正如你在上面看到的，我们创建了类型`Product`和方法`GetProducts`、`GetProductById()`和`CreateProduct()`。

### 产品列表

这将返回一个产品列表，看起来非常简单:

```
// ProductsGet.cs

using System;
using System.IO;
using System.Linq;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace Company.Function
{    
    public static class ProductsGet
    {
        [FunctionName("ProductsGet")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var json = JsonConvert.SerializeObject(new{
                products = Db.GetProducts()
            });

            return (ActionResult)new OkObjectResult(json);
        }
    }
} 
```

值得一提的是我们如何创建响应:

```
var json = JsonConvert.SerializeObject(new{
    products = Db.GetProducts()
}); 
```

我们在这里做的是创建一个 JSON 响应，如下所示:

```
{  "products":  [/*  list  of  products*/]  } 
```

同样值得注意的是我们如何将允许的 HTTP 动词限制为只有`get`。

### 获得特定产品

现在，这是关于要求一个特定的产品。我们将通过创建一条类似于`url/ProductGet/{productid}`的路线来实现。我们的想法是通过输入例如`url/ProductGet/1`来找到一个特定的产品。让我们来看看实现:

```
// ProductGet.cs

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace Company.Function
{
    public static class ProductGet
    {
        [FunctionName("ProductGet")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "ProductsGet/{id:int}")] HttpRequest req,
            int id,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var product = Db.GetProductById(id);
            var json = JsonConvert.SerializeObject(new {
                product = product
            });

            return (ActionResult) new OkObjectResult(json);
        }
    }
} 
```

特别注意我们如何在`HttpTrigger`装饰器中将`Route`的值设置为`ProductsGet/{id:int}`。这是一种模式，意味着我们将像`url/ProductsGet/1`一样响应请求。通过简单地输入`int id`，我们还可以从路线中解析出`id`。然后，我们简单地通过下面的代码过滤出匹配的产品，并创建我们的 JSON 响应:

```
var product = Db.GetProductById(id);
var json = JsonConvert.SerializeObject(new {
    product = product
}); 
```

### 创建产品

对于这一个，我们将把 HTTP 动词限制为`post`。我们还将从主体中读取并解析出`name`。代码如下所示:

```
// ProductCreate.cs

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace Company.Function
{
    public static class ProductCreate
    {
        [FunctionName("ProductCreate")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = null;

            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation("request body", requestBody);
            dynamic data = JsonConvert.DeserializeObject(requestBody);
            name = name ?? data?.name;

            if(name != null) 
            {
                log.LogInformation("name", name);
                var product = Db.CreateProduct(name);
                var json = JsonConvert.SerializeObject(new{
                    product = product
                });
                return (ActionResult)new OkObjectResult(json);
            } 
            else {
                return new BadRequestObjectResult("Missing name in posted Body");
            }
        }
    }
} 
```

这里有趣的部分是我们如何用这段代码解析出主体信息:

```
string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
log.LogInformation("request body", requestBody);
dynamic data = JsonConvert.DeserializeObject(requestBody);
name = name ?? data?.name; 
```

除此之外，代码非常简单，它检查主体中是否提供了`name`,如果是，继续创建它，最后创建一个 JSON 响应。

## 总结

好的，我们已经取得了一些进展。我们已经理解了选择无服务器的原因，能够创建两个不同的 Azure Function 应用程序，一个更简单，展示了处理查询参数和主体等概念，另一个例子展示了 REST API，我们接近于生产代码。

这是关于无服务器和 C#的第一篇文章，希望现在您已经掌握了基础知识。我们只是触及了表面。真正的价值在于无服务器框架能够设置不同的触发器和输入输出绑定，并与 Azure 上的许多其他服务进行交互。