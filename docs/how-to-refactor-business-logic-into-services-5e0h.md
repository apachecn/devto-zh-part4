# 如何将业务逻辑重构为服务

> 原文：<https://dev.to/makingloops/how-to-refactor-business-logic-into-services-5e0h>

唷老兄，你们这些控制者变胖了吗？

我有一个技巧，你可以用来快速减肥！

事情开始*变得*最常见的[方式是什么？](https://makingloops.com/fat-controller-causes/)

…

商业逻辑。

该死的是，在我们的控制器操作中大量使用商业逻辑脂肪太容易了。

那么我们能做些什么呢？

## 业务逻辑细化策略

您可以选择多种策略来处理控制器中的业务逻辑。仅举其中两个例子，您就可以:

**将逻辑移入可重用的`ActionFilter` s**

如果这个逻辑在其他控制器操作中是通用的，比如授权访问一个实体，那么这个逻辑就很好。然而，它仍然将业务逻辑与控制器相关的库耦合在一起。换句话说，你不能在 MVC 或 API 路径之外使用`ActionFilter`，这意味着你也不能在该领域之外使用该业务逻辑。也许你永远不会，所以在这种情况下，`ActionFilter`可能是一个很好的，务实的选择。

**将逻辑移动到注入控制器的服务中**

这种方式工作得很好，因为您的业务逻辑可以愉快地存在于它自己的可重用类中，而无需将其自身耦合到任何特定的客户端技术。

这就是我们在本文剩余部分要研究的策略。

## 将业务逻辑重构为服务

首先，让我们澄清术语。

**业务逻辑**

出于我们的目的，*业务逻辑*是实现业务目标的任何代码。对于电子商务应用程序来说，这可能是一些流程或任务，如创建订单、处理付款、创建和发送收据，以及添加或更新客户数据。

相比之下，*控制器*只负责管理传入的 HTTP 请求，委派满足请求的工作，并传递响应。

如果你在识别业务逻辑和控制器逻辑之间的界限(澄清责任)方面有困难，[在 Twitter 上给我发消息](https://twitter.com/joepetrakovich)，我会尽力帮助你。

**服务**

服务实际上是…提供服务的任何东西。

每当你从一个类调用另一个类的方法时，调用的类就是客户端，而带有方法的类就是服务。

现在你知道了，服务是一个带有可以被调用的方法的类。

将我们的业务逻辑放在自己的类中有很多好处。在控制器之外的领域使用该特性的能力，独立测试业务逻辑(和控制器)的能力，以及可读性方面的巨大改进，因为填充控制器的一个或多个逻辑块可以简化为明确命名的方法调用。

### 问题中的代码

好吧，我要把你扔出去一圈。

我们将要重构的代码实际上是一个 Azure 函数，而不是一个控制器。

不要害怕。让我解释一下。

我选择使用这个代码，因为它是真实的*真实世界*的生产代码，而不是一个虚构的例子。

最重要的是，用于构建 Azure 函数的代码与控制器非常相似，如果我不调用它，你几乎不会知道其中的区别。

我将指出一些细微的差别，但是本文中的重构概念仍然可以一对一地应用。

出于这个原因，在本文的剩余部分，我将交替使用单词*控制器*和*功能*。

*让我们看看代码……*

```
public static class HandleNotification
{  
    public static async Task<IActionResult> Run(
        HttpRequest req, 
        ILogger log, 
        ExecutionContext context)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var config = new ConfigurationBuilder()
            .SetBasePath(context.FunctionAppDirectory)
            .AddJsonFile("local.settings.json", optional: true, reloadOnChange: true)
            .AddEnvironmentVariables()
            .Build();

        var googleSheetsCredentialsPath = Path.Combine(context.FunctionAppDirectory, "credentials.json");

        var spreadsheetDefinition = (ID: config["SpreadsheetId"], Name: config["SpreadsheetName"], Range: config["SpreadsheetRange"]);

        var requestBody = await new StreamReader(req.Body).ReadToEndAsync();

        var googleSheetsService = GetGoogleSheetsService(googleSheetsCredentialsPath);

        log.LogInformation(requestBody);

        try
        {
            var jsonSerializerSettings = new JsonSerializerSettings { NullValueHandling = NullValueHandling.Ignore, ContractResolver = new DefaultContractResolver { NamingStrategy = new SnakeCaseNamingStrategy { } } };
            var samCartEvent = JsonConvert.DeserializeObject<SamCartEvent>(requestBody, jsonSerializerSettings);

            if (samCartEvent.Type == EventType.Order)
            {
                await AddOrderToSalesLogSpreadsheet(samCartEvent, spreadsheetDefinition, googleSheetsService);
            }
            else if (samCartEvent.Type == EventType.Refund)
            {
                await SetExistingOrderStatusToRefunded(samCartEvent, spreadsheetDefinition, googleSheetsService);
            }

            return new OkResult();
        }
        catch (Exception e)
        {
            log.LogError(e, e.Message);

            return new BadRequestObjectResult(e);
        }

    }

    private static async Task AddOrderToSalesLogSpreadsheet(SamCartEvent orderEvent, (string ID, string Name, string Range) spreadsheetDefinition, SheetsService sheetsService)
    {
        var customerName = $"{orderEvent.Customer.FirstName} {orderEvent.Customer?.LastName}";
        var programCity = orderEvent.Order.CustomFields?.SingleOrDefault(cf => cf.Name == "Program City")?.Value;

        var row = new List<object>() { orderEvent.Order.ID, customerName, orderEvent.Customer.Email, programCity, DateTime.Now.ToString("MM/dd/yyyy h:mm tt"), "Ordered" };

        var valueRange = new ValueRange();
        valueRange.Range = spreadsheetDefinition.Range;
        valueRange.Values = new List<IList<object>>();
        valueRange.Values.Add(row);

        var appendRequest = sheetsService.Spreadsheets.Values.Append(valueRange, spreadsheetDefinition.ID, spreadsheetDefinition.Range);
        appendRequest.ValueInputOption = SpreadsheetsResource.ValuesResource.AppendRequest.ValueInputOptionEnum.USERENTERED;

        var appendResponse = await appendRequest.ExecuteAsync();
    }

    private static async Task SetExistingOrderStatusToRefunded(SamCartEvent refundEvent, (string ID, string Name, string Range) spreadsheetDefinition, SheetsService sheetsService)
    {
        var refundedOrderID = refundEvent.Order.ID.ToString();

        var salesLogSpreadsheet = await sheetsService.Spreadsheets.Values.Get(spreadsheetDefinition.ID, spreadsheetDefinition.Range).ExecuteAsync();

        string statusCell = FindExistingOrderStatusCell(refundedOrderID, salesLogSpreadsheet, spreadsheetDefinition.Name);

        if (statusCell != null)
        {
            var row = new List<object>() { "Refunded" };

            var valueRange = new ValueRange();
            valueRange.Range = statusCell;
            valueRange.Values = new List<IList<object>>();
            valueRange.Values.Add(row);

            var updateRequest = sheetsService.Spreadsheets.Values.Update(valueRange, spreadsheetDefinition.ID, statusCell);
            updateRequest.ValueInputOption = SpreadsheetsResource.ValuesResource.UpdateRequest.ValueInputOptionEnum.USERENTERED;

            await updateRequest.ExecuteAsync();
        }

    }

    private static string FindExistingOrderStatusCell(string orderID, ValueRange salesLogSpreadsheet, string spreadsheetName)
    {
        string statusCell = null;

        for (int i = 0; i < salesLogSpreadsheet.Values?.Count; i++)
        {
            var row = salesLogSpreadsheet.Values[i];

            if ((string)row[0] == orderID)
            {
                statusCell = $"{spreadsheetName}!F{i+1}";
                break;
            }
        }

        return statusCell;
    }

    private static SheetsService GetGoogleSheetsService(string credentialsPath)
    {
        GoogleCredential credential;
        string[] _scopes = { SheetsService.Scope.Spreadsheets };

        using (var stream = new FileStream(credentialsPath, FileMode.Open, FileAccess.Read))
        {
            credential = GoogleCredential.FromStream(stream).CreateScoped(_scopes);
        }

        // Create Google Sheets API service.

        var sheetsService = new SheetsService(new BaseClientService.Initializer()
        {
            HttpClientInitializer = credential,
            ApplicationName = "SamCart Notification Listener"
        });

        return sheetsService;
    }
} 
```

### 比较 Azure 函数和 ASP.NET 控制器

您会马上注意到这段代码包含了一个类和一个返回`IActionResult`的方法。几乎等同于`Controller`的一个动作方法端点。

主要区别在于它被声明为静态的，并传递一些您可能不习惯的参数(我们将在重构中改变这一点)。

### 行为的简要说明

这段代码用作处理来自购物车 web 应用程序 SamCart 的销售通知的监听器。

您可以在 SamCart 的管理面板中配置一个端点 URL，它将在成功销售(或退款)时向其发布通知。这段代码监听这些通知，并使用 Google Sheets API 及其客户端库将它们记录到 Google Sheet 电子表格中。

### 攻坚重构

在之前的一篇文章中，我们研究了一些代码清理的策略。

在这个特殊的类中，我们有一个相当大的`Run`方法，它执行多个逻辑块的工作。

你能说出它们的名字吗？

…

设置配置，将 HTTP 请求体读取并解析到一个`SamCartEvent` POCO 中，并利用 Google Sheets API 在电子表格上添加新的销售订单或更新现有订单的退款状态。

为了简化这段代码，使它更容易阅读和修改，让我们试着把它分成更小的部分，并更紧密地遵循单一责任原则。

### 解析…

首先，我在写这篇文章的那一天发现 Azure 函数允许你受益于与`Controllers`提供的相同的自动模型绑定。我被原始的模板生成的代码引入歧途，您可以看到它正在手动解析 HTTP 请求体。

所以我们的第一个变化是完全消除解析逻辑，简单地声明一个`SamCartEvent`作为`Run`方法的第一个参数。

为了演示的清晰，我还将删除日志调用和 logger 参数。

```
public static async Task<IActionResult> Run(
    SamCartEvent samCartEvent, 
    ExecutionContext context)
{
    var config = new ConfigurationBuilder()
        .SetBasePath(context.FunctionAppDirectory)
        .AddJsonFile("local.settings.json", optional: true, reloadOnChange: true)
        .AddEnvironmentVariables()
        .Build();

    var googleSheetsCredentialsPath = Path.Combine(context.FunctionAppDirectory, "credentials.json");

    var spreadsheetDefinition = (ID: config["SpreadsheetId"], Name: config["SpreadsheetName"], Range: config["SpreadsheetRange"]);

    var googleSheetsService = GetGoogleSheetsService(googleSheetsCredentialsPath);

    try
    {
        if (samCartEvent.Type == EventType.Order)
        {
            await AddOrderToSalesLogSpreadsheet(samCartEvent, spreadsheetDefinition, googleSheetsService);
        }
        else if (samCartEvent.Type == EventType.Refund)
        {
            await SetExistingOrderStatusToRefunded(samCartEvent, spreadsheetDefinition, googleSheetsService);
        }

        return new OkResult();
    }
    catch (Exception e)
    {
        return new BadRequestObjectResult(e);
    }       
} 
```

这个问题解决后，我们只剩下两个主要任务。设置配置，并使用 Google Sheets API。

## 创建服务类

更深入地看，我们看到配置只由 Google Sheets API 使用，所以我们可以说设置它所涉及的工作实际上是沿着调用 API 的各种服务的相同逻辑块进行的。

出于这个原因，将所有代码移动到它自己的类中作为服务使用是有意义的。

为了做到这一点，我将创建一个新的类和方法，以这段代码实际上在做什么来命名。最终，我们将使用 Google Sheets 将我们的 SamCart 事件记录到一个电子表格中，因此我们将所有代码都移到了类`GoogleSheetsSamCartEventLogger`的方法`LogEvent`之后。

这里的一个关键区别是，在撰写本文时，没有办法注入导出配置文件位置所需的 ExecutionContext，因此第一行代码是一种替代方法。

剩下的和前面一样:获取电子表格配置，并根据`SamCartEvent`的类型调用适当的 API 端点。

```
public class GoogleSheetsSamCartEventLogger
{
    public async Task LogEvent(SamCartEvent samCartEvent)
    {
        var functionAppDirectory = Environment.GetEnvironmentVariable("AzureWebJobsScriptRoot")
                ?? (Environment.GetEnvironmentVariable("HOME") == null
                    ? Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location)
                    : $"{Environment.GetEnvironmentVariable("HOME")}/site/wwwroot");

        var config = new ConfigurationBuilder()
            .SetBasePath(functionAppDirectory)
            .AddJsonFile("local.settings.json", optional: true, reloadOnChange: true)
            .AddEnvironmentVariables()
            .Build();

        var googleSheetsCredentialsPath = Path.Combine(functionAppDirectory, "credentials.json");
        var spreadsheetDefinition = (ID: config["SpreadsheetId"], Name: config["SpreadsheetName"], Range: config["SpreadsheetRange"]);
        var googleSheetsService = GetGoogleSheetsService(googleSheetsCredentialsPath);

        if (samCartEvent.Type == EventType.Order)
        {
            await AddOrderToSalesLogSpreadsheet(samCartEvent, spreadsheetDefinition, googleSheetsService);
        }
        else if (samCartEvent.Type == EventType.Refund)
        {
            await SetExistingOrderStatusToRefunded(samCartEvent, spreadsheetDefinition, googleSheetsService);
        }

    }

    private async Task AddOrderToSalesLogSpreadsheet(SamCartEvent orderEvent, (string ID, string Name, string Range) spreadsheetDefinition, SheetsService sheetsService)
    {
        //...

    }

    private async Task SetExistingOrderStatusToRefunded(SamCartEvent refundEvent, (string ID, string Name, string Range) spreadsheetDefinition, SheetsService sheetsService)
    {
        //...

    }

    private string FindExistingOrderStatusCell(string orderID, ValueRange salesLogSpreadsheet, string spreadsheetName)
    {
        //...

    }

    private SheetsService GetGoogleSheetsService(string credentialsPath)
    {
        //...

    }
} 
```

创建了这个服务类之后，我们现在只需要在我们最初的`Run`方法中使用它。

最快的方法是直接在方法中“新建”。让我们先走这条路，然后我们将在本文中进一步改进它。

```
public async Task<IActionResult> Run(SamCartEvent samCartEvent)
{
    try
    {
        var eventLogger = new GoogleSheetsSamCartEventLogger();

        await eventLogger.LogEvent(samCartEvent);

        return new OkResult();
    }
    catch (Exception e)
    {
        return new BadRequestObjectResult(e);
    }
} 
```

现在我们看起来不错。

我们有一个更精简的方法，改变的理由更少(代码稳定性)。我们的方法只需要将工作交给一个服务，并用相关的结果进行响应。

现在更有可能是事件记录器会改变，而不是函数或控制器本身。

我们可以进一步改进这段代码吗？

前面我们提到了“更新”事件记录器。如果我们使用依赖注入，我们可以将选择的*责任转移到应用程序本身。*

选择的责任是我刚刚创造的一个术语，它包含了如何实现某个行为的决定。

就目前的情况来看，我们正在对听众使用 Google Sheets 进行硬编码。这意味着需要实际的代码更改和重新部署才能以不同的方式记录我们的事件。如果您没有计划或要求更改您的日志记录机制，这可能是非常好的，但是为了演示，假设我们的客户提到可能想要将日志记录移动到 Microsoft 的 Excel，或者一个普通的旧文本文件。

在这种情况下，允许*改变事件日志记录的实现*，而不必改变控制器，这将对我们有利。

变化越少=风险越小。

这就是接口和[依赖注入](https://makingloops.com/why-should-you-use-dependency-injection/)的结合派上用场的地方。

首先，我们可以从 Google Sheets 事件记录器中提取一个接口。

```
public interface ISamCartEventLogger
{
    Task LogEvent(SamCartEvent samCartEvent);
}

public class GoogleSheetsSamCartEventLogger : ISamCartEventLogger
{
    //...

} 
```

有了这些，我们现在可以自由地创建任意多的不同日志实现了。

作为一个例子，我们可以有一个像这样的非常原始的控制台日志记录器。

```
public interface ISamCartEventLogger
{
    Task LogEvent(SamCartEvent samCartEvent);
}

public class ConsoleSamCartEventLogger : ISamCartEventLogger
{
    public async Task LogEvent(SamCartEvent samCartEvent) 
    {
        if (samCartEvent.Type == EventType.Order)
        {
            Console.WriteLine("An order was placed!");
        }
        else if (samCartEvent.Type == EventType.Refund)
        {
            Console.WriteLine("Aww... a refund.");
        }
    }
} 
```

接口很有趣，但是如果没有依赖注入，我们仍然需要修改控制器的`Run`方法的代码来“更新”替代实现。

还记得这个吗？

```
public async Task<IActionResult> Run(SamCartEvent samCartEvent)
{
    try
    {
        var eventLogger = new ConsoleSamCartEventLogger();

        await eventLogger.LogEvent(samCartEvent);

        //...

    }
    //...

} 
```

相反，让我们利用 ASP.NET 提供的内置依赖注入*容器*，将实现决策转移到应用程序启动配置中。

在 Azure 函数中这样做与在 ASP.NET MVC 或 Web API 项目中这样做非常相似，你只需在 Startup.cs 类中注册你的类型，然后通过构造函数参数使用它们。

```
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddScoped<ISamCartEventLogger, GoogleSheetsSamCartEventLogger>();
    }
}

public class HandleNotification
{
    private readonly ISamCartEventLogger eventLogger;

    public HandleNotification(ISamCartEventLogger eventLogger)
    {
        this.eventLogger = eventLogger;
    }

    public async Task<IActionResult> Run(SamCartEvent samCartEvent)
    {
        try
        {
            await eventLogger.LogEvent(samCartEvent);
        }
        //...

    }
} 
```

随着它的移动，我们不再需要改变控制器的`Run`方法，从而提高了代码的稳定性。

我们还极大地增强了每个组件的可读性。将上面的代码片段与文章开头的原始代码进行比较。