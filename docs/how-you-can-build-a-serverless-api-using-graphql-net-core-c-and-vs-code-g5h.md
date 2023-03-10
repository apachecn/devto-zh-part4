# 如何使用 GraphQL 构建无服务器 API？Net 核心，C#和 VS 代码

> 原文：<https://dev.to/azure/how-you-can-build-a-serverless-api-using-graphql-net-core-c-and-vs-code-g5h>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 本文将带您了解如何使用 GraphQL 构建一个完整的 CRUD API。我们最终将它托管在一个无服务器的函数中，并展示了如何进行外部 HTTP 调用来构建 API。数据存储在这里还是其他地方并不重要。GraphQL 是你的用户将与之对话的前台。

TLDR；这篇文章可能有点长，但它确实在 GraphQL、查询、突变方面教了很多东西。它还教你如何在。Net 核心，C#和 VS 代码

在本文中，我们将:

*   **了解**如何构建一个完整的 CRUD GraphQL，包括查询和变异
*   **创建**一个无服务器函数，并在函数中托管我们的 GraphQL API
*   向展示如何进行外部 HTTP 调用，并使之成为我们的 GraphQL API 的一部分

## 资源

*   [在中开始使用 GraphQL。Net](https://dev.to/dotnet/learn-how-you-can-use-graphql-in-net-core-and-c-4h96?wt.mc_id=devto-blog-chnoring)
    这是我用. Net 写的第一篇关于 GraphQL 的文章，我推荐你看一看，感受一下 GraphQL 的基础知识。

*   [图表 QL](https://graphql.org/)

*   [从 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?wt.mc_id=devto-blog-chnoring)
    开始讲述如何创建一个无服务器函数，并使用 VS 代码部署到云中

*   [从无服务器开始。](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-class-library?wt.mc_id=devto-blog-chnoring)
    网。Azure 函数的. Net C#开发人员参考

*   用 C#和创建一个无服务器的 API。Net
    我写的一篇文章，讲述了如何创建一个无服务器的 CRUD API。Net、C#和 VS 代码

*   [创建你的第一个无服务器函数](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/?wt.mc_id=devto-blog-chnoring)
    学习模块用于创建 Azure 函数

*   免费 Azure 账户
    要部署无服务器 Azure 功能，你需要一个免费的 Azure 账户

## 创建无服务器功能 app

我们要做的第一件事是创建一个无服务器函数。但是什么是无服务器的，为什么它在 GraphQL 的上下文中有用？这实际上是两个问题，但让我们试着按顺序回答。

### 为什么无服务器？

无服务器不是没有服务器，而是服务器不再在你的地下室里。简而言之，服务器已经转移到了云端。然而，这还不是全部。*无服务器*已经意味着其他事情，即一切都为你准备好了。这意味着你不需要考虑你的代码在什么操作系统上运行或者在什么网络服务器上运行，一切都是由*管理的*。还有更多。

> 总是有更多的，不是吗？现在什么是花式贴纸？

是成本的问题。无服务器通常意味着便宜。

> 为什么便宜？

嗯，无服务器代码意味着很少运行，你只需为它执行的时间付费

> 好吧，你是怎么赚钱的？

这个函数并不总是存在的。当有人/事查询你的函数时，无论需要什么资源都会被分配。

> 这难道不会使它变得有点慢吗？就像在它争先恐后地创建然后响应查询时，可能会有一些初始等待？

你是对的。这就是所谓的冷启动。然而，这可以通过定期轮询我们的功能或使用缩短*冷启动*的额外服务来避免。

> 好吧，所以我可以要么有 100%的可用性或便宜，选择一个:)

我想是的。

### 为什么用 GraphQL 做 Serverless？

好吧，我们在无服务器的*是什么*和*为什么*上更聪明一点，那么为什么要加入 GraphQL 呢？

GraphQL 有能力将来自不同 API 的数据缝合在一起，因此它可以作为一个 API 来聚合来自不同来源的数据。所以在某种意义上，它在无服务器的环境中工作得很好，因为如果它是别人的 API，你就不需要在函数中存储任何数据。

> 好的，听起来不错，我想没有其他好的理由了，剩下的只是关于 GraphQL 和无服务器的宣传，对吗？；)

...

### 先决条件

要创建一个无服务器功能，我们首先需要一个功能应用程序来放置它。为了尽可能简单，我们将使用 VS 代码和 Azure 函数扩展。所以我们的先决条件是:

1.  节点. js
2.  Visual Studio 代码
3.  Azure 功能扩展

我们可以从以下页面下载 Node.js:

> [https://nodejs.org/en/](https://nodejs.org/en/)

您可以在此处找到 Visual Studio 代码:

> [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

至于我们需要的扩展。搜索名为`Azure Functions`的扩展名。它应该如下所示:

[![](img/dd6c33ef6c202bae8aba94a796db46fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8qJJfvI7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tks3j4sv3xpr327u4zwd.png)

### 脚手架

好了，现在我们应该已经准备好创建我们的第一个无服务器函数了。如前所述，函数需要存在于一个叫做函数应用的东西中。所以我们需要先创建功能 app。

点击`CMD+SHIFT+P`或选择`View/Command Palette`调出命令面板:

现在，我们需要键入一个命令来帮助我们创建一个无服务器功能应用程序。它叫做`Azure Functions: Create New Project`

然后，它会询问您创建应用程序的目录。你所在的目录是默认选择的，选择它。

接下来询问的是语言，选择`C#`。

接下来，它会询问您有关“项目优先”功能以及应该如何触发它。选择`HttpTrigger`。

现在您需要提供一个函数名。称之为 Graphql。

下一个问题是名称空间。在这里你真的可以选择任何东西，但是让我们仅仅为了它而称它为`Function`。

最后，它询问了关于`Access Right`的问题。这里有不同的选项`Anonymous`、`Function`、`Admin`。我们选择`Anonymous`,因为我们希望公开我们的功能。其他选项意味着我们需要在调用函数时提供某种键。

VS 代码现在应该支持所有需要的文件。它还应该要求您`restore`所有的依赖项，以下载库。你也可以运行

```
dotnet restore 
```

如果你错过了点击这个对话框。

您的项目结构现在应该如下所示:

[![](img/b1afd925517b311395b44d04bddef1e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MszXKBO7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5mf9mzd32g2fmygbmnir.png)

### 测试出来

让我们确保我们可以运行新搭建的功能。首先，如果您想要添加必要的以便能够调试，您应该已经得到提示。你应该在这里回答`YES`。这将生成如下所示的目录`.vscode`:

[![](img/c5d4a12e616a269d7be77b286eacddbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVC1KGZF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qgttl2qmmzhbabtyxdbm.png)

`tasks.json`包含构建、清理和运行项目所需的任务。这些将帮助您完成我们接下来要做的步骤`Debugging`。

为了调试，我们从菜单中选择`Debug/Start Debugging`。它应该编译代码，完成后应该是这样的:

[![](img/d57eb5abd24ff623cbba7ef9b94a6a89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HeMdbxhK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uq3crqqdhp98y1kun069.png)

它告诉我们去`http://localhost:7071/api/Graphql`

让我们启动一个浏览器:

[![](img/6b49830afd6b6935b15eac4f4930f731.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ip6bZmEs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j4mb132acmcat6wjtvf7.png)

似乎工作正常。:)

它击中了我们的功能`Graphql`。说到这里，让我们快速看一下搭建无服务器应用程序时给出的示例代码:

```
// Graphql.cs

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace Function
{
    public static class Graphql
    {
        [FunctionName("Graphql")]
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

让我们现在不要花太多时间去理解每一件事，但可以说它完成了它的工作，并且能够使用查询参数:

```
string name = req.Query["name"]; 
```

和正文，以防你发布请求:

```
string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
dynamic data = JsonConvert.DeserializeObject(requestBody);
name = name ?? data?.name; 
```

接下来，我们来谈谈 GraphQL。

## 向无服务器添加 GraphQL

好了，我们确定了想要将 GraphQL 添加到我们的无服务器功能中。首先，让我们构建一个 GraphQL API。任何 GraphQL 都将包含相同的活动部分:

*   **一个模式**，它将定义我们可以查询的内容以及我们拥有的自定义数据类型
*   **解析器**，能够响应请求并最终交付响应的函数集合

### 添加一个 GraphQL 模式

这将会用一种叫做 GQL 或者 G raphQL **Q** 查询 **L** 的语言来撰写。我们的模式将如下所示:

```
type Jedi {
  id: ID
  name: String,
  side: String
}

input JediInput {
  name: String
  side: String
  id: ID
}

type Mutation {
  addJedi(input: JediInput): Jedi
  updateJedi(input: JediInput ): Jedi
  removeJedi(id: ID): String
}

type Query {
    jedis: [Jedi]
    jedi(id: ID): Jedi
    hello: String
} 
```

这已经很多了。让我们解释一下我们在看什么。

**查询**

任何类型为`Query`或`Mutation`的东西都是我们可以在对 API 的查询中要求的。`Query`的语义是你要*取*数据。在本例中，我们希望使用如下查询语法获取一个 jedis 列表:

```
{
  jedis { name side }
} 
```

这相当于用 SQL 编写以下代码:

```
SELECT name, side
FROM jedis; 
```

我们做的另一件事是支持带有参数的查询，即`jedi(id: ID): Jedi`。我们这样称呼它:

```
{ 
  jedi(id: 1) { name }
} 
```

这相当于用 SQL 编写以下代码:

```
SELECT name
FROM jedi
WHERE id=1; 
```

**自定义类型**

所有可查询的内容都在`type Query`下定义。除了`type Mutation`之外，其他的都是我们定义的自定义类型。比如:

```
type Jedi {
  id: ID
  name: String,
  side: String
} 
```

**突变**

这在语义上意味着我们将尝试更改数据。看看我们支持的操作:

```
addJedi(input: JediInput): Jedi
updateJedi(input: JediInput ): Jedi
removeJedi(id: ID): String 
```

我们可以看到我们支持添加、更新和删除。

要调用一个突变，我们需要像这样输入:

```
mutation test { 
  addJedi(input: { 
    name: "JarJar", 
    side: "Dark"  
  }) { name } 
} 
```

**输入，我们突变的复杂输入**

注意`addJedi()`中的输入属性`input`。如果我们查看这个模式，我们可以看到它的类型是`JediInput`，定义如下:

```
input JediInput {
  name: String
  side: String
  id: ID
} 
```

我们使用关键字`input`而不是`type`的原因是这是一个特例。怎么特别，你想知道吗？突变有两种输入参数:

1.  **标量**，这是字符串、ID、布尔等，也叫原语
2.  **输入**，这只不过是一个复杂的数据类型，上面有许多属性，例如`JediInput`

所以绝对有可能这样定义一个突变:

```
type Mutation {
  addTodo(todo: String!): String
} 
```

所以最重要的问题是为什么我不能使用像`Jedi`这样的自定义类型作为我的变异的输入参数类型呢？

> 诚实的回答是我不知道。

只要记住这一点:如果你需要一个比标量更复杂的输入参数，那么你需要这样定义它:

```
input MyInputType {
  // my columns
} 
```

**添加 NuGet 包**

好了，下一步是用代码正确地设置这个模式。为此，我们将创建一个文件`Server.cs`，并安装 GraphQL 包，如下所示:

```
dotnet add package GraphQL 
```

**创建一个模式**

现在将下面的代码添加到`Server.cs`中，就像这样:

```
using GraphQL;
using GraphQL.Types;
using Newtonsoft.Json;
using System.Threading.Tasks;

namespace Function {
  public class Server 
  {
    private ISchema schema { get; set; }
    public Server() 
    {
      this.schema = Schema.For(@"
          type Jedi {
            id: ID
            name: String,
            side: String
          }

          input JediInput {
            name: String
            side: String
            id: ID
          }

          type Mutation {
            addJedi(input: JediInput): Jedi
            updateJedi(input: JediInput ): Jedi
            removeJedi(id: ID): String
          }

          type Query {
              jedis: [Jedi]
              jedi(id: ID): Jedi
          }
      ", _ =>
      {
        _.Types.Include<Query>();
        _.Types.Include<Mutation>();
      });

    }

    public async Task<string> QueryAsync(string query) 
    {
      var result = await new DocumentExecuter().ExecuteAsync(_ =>
      {
        _.Schema = schema;
        _.Query = query;
      });

      if(result.Errors != null) {
        return result.Errors[0].Message;
      } else {
        return JsonConvert.SerializeObject(result.Data);
      }
    }
  }
} 
```

在上面的构造函数中，我们通过调用`Schema.For()`来设置模式，用一个字符串表示我们的模式，用 GQL 表示。第二个参数不会被编译，也就是这个部分:

```
_ =>
  {
    _.Types.Include<Query>();
    _.Types.Include<Mutation>();
  } 
```

### 添加解析器

它不能编译的原因是`Query`和`Mutation`还不存在。它们只是响应查询和变异请求的解析器类。让我们通过创建第一个`Db.cs`和一个文件`Query.cs`来编译它

**添加内存数据库**

```
// Db.cs

using System.Collections.Generic;
using System.Linq;

namespace Function
{
  public class StarWarsDB
  {
    private static List<Jedi> jedis = new List<Jedi>() {
      new Jedi(){ Id = 1, Name ="Luke", Side="Light"},
      new Jedi(){ Id = 2, Name ="Yoda", Side="Light"},
      new Jedi(){ Id = 3, Name ="Darth Vader", Side="Dark"}
    };
    public static IEnumerable<Jedi> GetJedis()
    {
      return jedis;
    }

    public static Jedi AddJedi(Jedi jedi)
    {
      jedi.Id = jedis.Count + 1;
      jedis.Add(jedi);
      return jedi;
    }

    public static Jedi UpdateJedi(Jedi jedi)
    {
      var toUpdate = jedis.SingleOrDefault(j => j.Id == jedi.Id);
      toUpdate.Name = jedi.Name;
      toUpdate.Side = jedi.Side;
      return toUpdate;
    }

    public static string RemoveJedi(int id)
    {
      var toRemove = jedis.SingleOrDefault(j => j.Id == id);
      jedis.Remove(toRemove);
      return "success";
    }
  }

  public class Jedi
  {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Side { get; set; }
  }
} 
```

`Db.cs`无非是一个简单的内存数据库。

**添加一个解析器类来处理所有查询**

接下来，让我们创建`Query.cs` :

```
// Query.cs

using System.Collections.Generic;
using GraphQL;
using System.Linq;

namespace Function
{
  public class Query
  {
    [GraphQLMetadata("jedis")]
    public IEnumerable<Jedi> GetJedis()
    {
      return StarWarsDB.GetJedis();
    }

    [GraphQLMetadata("jedi")]
    public Jedi GetJedi(int id)
    {
      return StarWarsDB.GetJedis().SingleOrDefault(j => j.Id == id);
    }

    [GraphQLMetadata("hello")]
    public string GetHello()
    {
      return "World";
    }
  }

} 
```

关于上述内容，有趣的是我们如何将 GraphQL 模式中的一些内容映射到一个解析器函数。为此，我们像这样使用装饰器`GraphQLMetadata`:

```
[GraphQLMetadata("jedis")]
public IEnumerable<Jedi> GetJedis()
{
  return StarWarsDB.GetJedis();
} 
```

上面告诉我们，如果用户查询`jedis`，那么函数`GetJedis()`将会响应。

处理参数几乎一样容易。这是同一个装饰器，但是我们只是像这样添加输入参数:

```
[GraphQLMetadata("jedi")]
public Jedi GetJedi(int id)
{
  return StarWarsDB.GetJedis().SingleOrDefault(j => j.Id == id);
} 
```

**添加一个解析器类来处理所有的突变请求**

我们就快完成了，但是我们需要类`Mutation.cs`，让我们向它添加以下内容:

```
// Mutation.cs

using GraphQL;

namespace Function {
  public class Mutation
  {
    [GraphQLMetadata("addJedi")]
    public Jedi AddJedi(Jedi  input) 
    {
      return StarWarsDB.AddJedi(input);
    }

    [GraphQLMetadata("updateJedi")]
    public Jedi UpdateJedi(Jedi input)
    {
      return StarWarsDB.AddJedi(input);
    }

    [GraphQLMetadata("removeJedi")]
    public string AddJedi(int id)
    {
      return StarWarsDB.RemoveJedi(id);
    }
  }
} 
```

如你所见，它看起来与`Query.cs`非常相似，即使是参数处理。

**更新我们的无服务器功能**

这个难题只剩下一部分，即我们的无服务器功能。它需要被改变，这样我们就可以支持用户像这样调用我们的函数:

```
url?query={ jedis } { name } 
```

将`Graphql.cs`中的代码修改为:

```
 using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace Function
{
    public static class GraphQL
    {
        [FunctionName("GraphQL")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var server = new Server();
            string query = req.Query["query"];
            // string query = "mutation test { addJedi(input: { name: \"JarJar\", side: \"Dark\"  }) { name } }";

            var json = await server.QueryAsync(query);
            return new OkObjectResult(json);
        }
    }
} 
```

### 全部测试出来

为了测试它，我们只需从菜单中选择`Debug/Start Debugging`开始调试，并将浏览器中的 URL 改为:

```
http://localhost:7071/api/GraphQL?query={ jedis { name } } 
```

让我们看看浏览器是怎么说的:

[![](img/48dfa84c94a4ddb7b73da0e77fb5b747.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S877VOSJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m5t7d3aiwggazkpl7den.png)

[![](img/9cb5747fe8804fb6f090ddaa8f0e1054.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0hsQEXFw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/76xk54ulkvlggtgsrq8b.gif)

是的，我们做到了。具有 GraphQL API 的无服务器功能。

## 奖励-呼叫其他端点

现在。GraphQL 的一大优点是它允许我们调用其他 API，从而使 GraphQL 充当一个聚合层。我们可以通过以下步骤轻松实现这一目标:

1.  **做 HTTP 请求**，这应该对我们的外部 API 做请求
2.  **为我们的*外部*调用添加解析器方法**
3.  **用新类型更新我们的模式**
4.  **测试**出来吧

### HTTP 请求

我们可以很容易地在。所以让我们创建一个类`Fetch.cs`，就像这样:

```
// Fetch.cs

using System.Net.Http;
using System.Threading.Tasks;

namespace Function {
  public class Fetch
  {
    private static string BaseUrl = "https://swapi.co/api";
    public static async Task<string> ByUrl(string url)
    {
      using (var client = new HttpClient())
      {
        var json = await client.GetStringAsync(string.Format("{0}/{1}", BaseUrl, url));

        return json;
      }
    }
  }
} 
```

### 添加解析器的方法

现在打开`Query.cs`并将下面的方法添加到`Query`类中:

```
[GraphQLMetadata("planets")]
  public async Task<List<Planet>> GetPlanet() 
  {
    var planets = await Fetch.ByUrl("planets/");
    var result = JsonConvert.DeserializeObject<Result<List<Planet>>>(planets);
    return result.results;
  } 
```

此外，我们应该安装一个新的 NuGet 包:

```
dotnet add package Newtonsoft.Json 
```

这是必需的，这样我们就可以将得到的 JSON 响应转换成 Poco。

我们还应该将类型`Planet`和`Result`添加到`Query.cs`中，但是在类定义之外:

```
public class Result<T>
{
  public int count { get; set; }
  public T results { get; set; }
}

public class Planet
{
  public string name { get; set; }
} 
```

### 用新类型更新我们的模式

在尝试之前，我们还需要更新模式。因此，让我们打开`Schema.cs`，并确保它现在看起来像这样:

```
this.schema = Schema.For(@"
          type Planet {
            name: String
          }

          type Jedi {
            id: ID
            name: String,
            side: String
          }

          input JediInput {
            name: String
            side: String
            id: ID
          }

          type Mutation {
            addJedi(input: JediInput): Jedi
            updateJedi(input: JediInput ): Jedi
            removeJedi(id: ID): String
          }

          type Query {
              jedis: [Jedi]
              jedi(id: ID): Jedi
              hello: String
              planets: [Planet]
          }
      ", _ =>
      {
        _.Types.Include<Query>();
        _.Types.Include<Mutation>();
      }); 
```

上面我们添加了类型`Planet` :

```
type Planet {
  name: String
} 
```

我们还在`Query`中添加了`planets`:

```
planets: [Planet] 
```

### 试探一下

应该是这样，我们来测试一下。`Debug/Start Debugging` :

```
http://localhost:7071/api/GraphQL?query={ planets { name } } 
```

[![](img/9e612be54c85c2288b6e912a21b72511.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DHjrqppa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dr5i0mqk4djyhl4yskak.png)

我们找到了。:)

我们成功地对 API 进行了外部调用，并使其成为我们的 GraphQL API 的一部分。正如你所看到的，我们可以很容易地混合不同来源的数据。

## 总结

我们已经成功创建了一个支持完整 CRUD 的 GraphQL。也就是说，我们既支持数据查询，也支持创建、更新和删除。

此外，我们已经在无服务器功能方面迈出了第一步。

最后，我们添加了由无服务器函数提供服务的 GraphQL API。

作为额外的收获，我们还展示了如何采用外部 API，并使它们成为我们 API 的一部分。这是非常强大的东西。

我希望你喜欢这篇文章。在下一部分中，我们将看看如何在 Web API 项目中添加 GraphQL。网芯。