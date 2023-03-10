# 用 C#/创建一个不和谐机器人。Net Core 和 DSharpPlus

> 原文：<https://dev.to/bizzycola/creating-a-discord-bot-with-c-net-core-and-dsharpplus-1obg>

## 简介

今天我将向你展示如何使用 C#来设置一个基本的不和谐机器人。net core 和 DSharpPlus。我们将使用反射来自动加载包含您的命令的模块类。

所以，首先，对于那些以前用 C#构建过不和谐机器人的人，你可能会问为什么我没有选择使用 Discord.Net(更流行的不和谐库)。Discord.Net 是一个伟大的图书馆，相比之下它有自己的优点和缺点。

我喜欢 DSharpPlus 的原因是它的交互式库可以让你很容易地等待用户的输入和反应，以及(这并不重要，只是我喜欢)发送“输入”的便利性消息放在实际内容之前。当您的命令可能需要一点时间时，这很有用，因为“输入...”指示器向用户显示你的机器人已经收到命令并正在处理结果。

**注意**这篇文章也在我的[博客](https://lchant.com/Blog/creating-a-discord-bot-with-cnet-core-and-dsharpplus)上。

## 创建 discord 应用

因此，要使用 create bot，我们必须首先前往 [Discord 开发者门户](https://discordapp.com/developers/applications/)并创建一个应用程序。

点击**新应用**按钮，并给你的应用命名(你可以稍后设置你的机器人昵称和头像，现在只需按你喜欢的命名并点击创建)。

现在您将看到一个页面，您可以在其中编辑您的应用程序名称和描述。你可以随意这样做，然后在左边的菜单上，点击“机器人”链接。

现在，您将拥有一个几乎空白的页面，可以选择向该应用程序添加一个机器人。点击**添加机器人**并在它要求你确认时点击“是”。您可以在这里设置您的机器人用户名和个人资料图片。保留此页面，稍后您将需要来自 bot 页面的令牌以及来自一般信息页面的客户端 ID 和令牌。

好了，现在，要将机器人添加到您的 discord 服务器(您必须拥有管理服务器权限才能添加机器人)，请在以下 url 中更改 CLIENTIDHERE，然后在登录 discord 时在您的浏览器中访问它。选择你的服务器并添加机器人:[https://discordapp.com/api/oauth2/authorize?client _ id = clientid here&scope = bot&permissions = 3072](https://discordapp.com/api/oauth2/authorize?client_id=CLIENTIDHERE&scope=bot&permissions=3072)

## 创建项目

我假设如果你正在阅读本教程，你已经有了一个编辑器(比如 VS 或者 VS Code 等)并且有了。net core 安装(此时我相信是 2.2)。

首先转到您希望创建项目的目录，并键入`dotnet new console`。此时，如果您愿意，您可以通过同时运行`dotnet restore`、`dotnet build`和`dotnet run`来确保它正常工作。

在我们开始编码之前，我们还将安装 DSharpPlus 依赖项。在本指南中，我将向您展示如何使用 DSharpPlus 的命令 Next 和交互模块，因此我们将继续使用以下命令添加这些模块:

```
dotnet add package DSharpPlus
dotnet add package DSharpPlus.CommandsNext
dotnet add package DSharpPlus.Interactivity 
```

现在，我们还将添加微软的配置库，以方便加载 JSON 配置文件，我们将在其中存储我们的 Discord 连接细节。像这样添加包:

```
dotnet add package Microsoft.Extensions.Configuration
dotnet add package Microsoft.Extensions.Configuration.Binder
dotnet add package Microsoft.Extensions.Configuration.Json 
```

在这一点上，你也可以再次运行`dotnet restore`,但是当你添加包的时候，dotnet 可能会帮你做这件事。

## 获取代码

好的，所以 DSharpPlus 和 Discord.Net 都使用异步代码。现在新版本的 C#允许你让你的 main 方法异步，所以这应该不是太大的问题。

让我们从 Program.cs 开始。如果您愿意，可以在继续之前随意将该文件/类重命名为其他名称。

```
using System;
using System.Linq;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using DSharpPlus;
using DSharpPlus.CommandsNext;
using DSharpPlus.Interactivity;
using Microsoft.Extensions.Configuration;

internal class Program
{
    /* This is the cancellation token we'll use to end the bot if needed(used for most async stuff). */
    private CancellationTokenSource _cts { get; set; }

    /* We'll load the app config into this when we create it a little later. */
    private IConfigurationRoot _config;

    /* These are the discord library's main classes */
    private DiscordClient _discord;
    private CommandsNextModule _commands;
    private InteractivityModule _interactivity;

    /* Use the async main to create an instance of the class and await it(async main is only available in C# 7.1 onwards). */
    static async Task Main(string[] args) => await new Program().InitBot(args);

    async Task InitBot(string[] args)
    {
        try
        {
            Console.WriteLine("[info] Welcome to my bot!");
            _cts = new CancellationTokenSource(); 

            // Load the config file(we'll create this shortly)
            Console.WriteLine("[info] Loading config file..");
            _config = new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("config.json", optional: false, reloadOnChange: true)
                .Build();

            // Create the DSharpPlus client
            Console.WriteLine("[info] Creating discord client..");
            _discord = new DiscordClient(new DiscordConfiguration
            {
                Token = _config.GetValue<string>("discord:token"),
                TokenType = TokenType.Bot
            });

            // Create the interactivity module(I'll show you how to use this later on)
            _interactivity = _discord.UseInteractivity(new InteractivityConfiguration()
            {
                PaginationBehaviour = TimeoutBehaviour.Delete, // What to do when a pagination request times out
                PaginationTimeout = TimeSpan.FromSeconds(30), // How long to wait before timing out
                Timeout = TimeSpan.FromSeconds(30) // Default time to wait for interactive commands like waiting for a message or a reaction
            });

            // Build dependancies and then create the commands module.
            var deps = BuildDeps();
            _commands = _discord.UseCommandsNext(new CommandsNextConfiguration
            {
                StringPrefix = _config.GetValue<string>("discord:CommandPrefix"), // Load the command prefix(what comes before the command, eg "!" or "/") from our config file
                Dependencies = deps // Pass the dependancies
            });

            // TODO: Add command loading!

            RunAsync(args).Wait();
        }
        catch(Exception ex)
        {
            // This will catch any exceptions that occur during the operation/setup of your bot.

            // Feel free to replace this with what ever logging solution you'd like to use.
            // I may do a guide later on the basic logger I implemented in my most recent bot.
            Console.Error.WriteLine(ex.ToString());
        }
    }

     async Task RunAsync(string[] args)
    {
        // Connect to discord's service
        Console.WriteLine("Connecting..");
        await _discord.ConnectAsync();
        Console.WriteLine("Connected!");

        // Keep the bot running until the cancellation token requests we stop
        while (!_cts.IsCancellationRequested)
            await Task.Delay(TimeSpan.FromMinutes(1));
    }

    /* 
     DSharpPlus has dependancy injection for commands, this builds a list of dependancies. 
     We can then access these in our command modules.
    */
    private DependencyCollection BuildDeps()
        {
            using var deps = new DependencyCollectionBuilder();

            deps.AddInstance(_interactivity) // Add interactivity
                .AddInstance(_cts) // Add the cancellation token
                .AddInstance(_config) // Add our config
                .AddInstance(_discord); // Add the discord client

            return deps.Build();
        }
} 
```

哇，代码真多！我添加了一些评论来帮助你理解正在发生的事情(如果你对本指南的任何一点感到困惑，请在评论中告诉我，如果需要，我可以用更多的信息来更新它)。

## 添加配置文件

好的，如果你运行这个机器人，你会发现一些问题。首先，我们还没有配置文件，所以让我们创建一个。

在您的项目目录中，创建一个名为`config.json`的文件。
现在，将以下代码放入文件中，并用您在 Discord 开发人员门户上找到的值替换这些值(除了命令前缀，在您的命令前将其设置为您想要的字符。或许“！”、“$”或“/”都可以):

```
{
  "discord": {
    "token": "BOT TOKEN HERE",
    "appId": "CLIENT ID HERE",
    "appSecret": "CLIENT SECRET HERE",
    "CommandPrefix":  "/" 
  }
} 
```

现在，我们需要在构建项目时将这个配置文件复制到我们的输出目录中。打开。csproj 文件，并在标记内添加以下 ItemGroup:

```
<ItemGroup>
  <None Update="config.json" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup> 
```

如果您不想让它仅在文件更新时拷贝，您也可以将“CopyToOutputDirectory”设定为“总是”。

## 添加命令

好了，现在我们有了配置，我们的机器人可能会启动，但现在我们需要一种与之交互的方式。

在项目中创建一个名为“命令”(或模块)的新目录。在该目录中，创建一个名为“IModule.cs”的文件。这是我们的命令将扩展的接口，所以我们可以通过反射代码加载它们。只需创建一个名为“IModule”的空接口，如下所示:

```
public interface IModule {} 
```

现在，在命令目录中创建另一个文件，并在该模块中将其命名为`BasicCommands.Module.cs`
，添加以下代码:

```
using DSharpPlus.CommandsNext;
using DSharpPlus.CommandsNext.Attributes;
using DSharpPlus.Entities;
using DSharpPlus.Interactivity;
using System;
using System.Threading.Tasks;

/* Create our class and extend from IModule */
public class BasicCommandsModule : IModule
{
    /* Commands in DSharpPlus.CommandsNext are identified by supplying a Command attribute to a method in any class you've loaded into it. */
    /* The description is just a string supplied when you use the help command included in CommandsNext. */
    [Command("alive")]
    [Description("Simple command to test if the bot is running!")]
    public async Task Alive(CommandContext ctx)
    {
        /* Trigger the Typing... in discord */
        await ctx.TriggerTypingAsync();

        /* Send the message "I'm Alive!" to the channel the message was recieved from */
        await ctx.RespondAsync("I'm alive!");
    }
} 
```

现在回到 Program.cs，我们将添加一些有趣的反射代码来加载从我们的 IModule 接口扩展的任何内容。

在 InitBot 方法中，在`RunAsync(args).wait();`上方添加以下代码:

```
Console.WriteLine("[info] Loading command modules..");

var type = typeof(IModule); // Get the type of our interface
var types = AppDomain.CurrentDomain.GetAssemblies() // Get the assemblies associated with our project
    .SelectMany(s => s.GetTypes()) // Get all the types
    .Where(p => type.IsAssignableFrom(p) && !p.IsInterface); // Filter to find any type that can be assigned to an IModule

var typeList = types as Type[] ?? types.ToArray(); // Convert to an array
foreach (var t in typeList)
    _commands.RegisterCommands(t); // Loop through the list and register each command module with CommandsNext

Console.WriteLine($"[info] Loaded {typeList.Count()} modules."); 
```

好，现在运行`dotnet build`和`dotnet run`。你的机器人应该上线，如果你输入你的命令前缀，然后活着，你应该会看到一些输出(例如/: `/alive`)！

## 交互性

因此，正如我前面提到的，我喜欢 DSharpPlus 在命令执行后等待用户输入的能力。我将向您展示一个如何等待用户消息并阅读它的基本示例。

回到 BasicCommands 模块，添加这个新方法:

```
[Command("interact")]
[Description("Simple command to test interaction!")]
public async Task Interact(CommandContext ctx)
{
    /* Trigger the Typing... in discord */
    await ctx.TriggerTypingAsync();

    /* Send the message "I'm Alive!" to the channel the message was recieved from */
    await ctx.RespondAsync("How are you today?");

    var intr = ctx.Client.GetInteractivityModule(); // Grab the interactivity module
    var reminderContent = await intr.WaitForMessageAsync(
        c => c.Author.Id == ctx.Message.Author.Id, // Make sure the response is from the same person who sent the command
        TimeSpan.FromSeconds(60) // Wait 60 seconds for a response instead of the default 30 we set earlier!
    );
    // You can also check for a specific message by doing something like
    // c => c.Content == "something"

    // Null if the user didn't respond before the timeout
    if(reminderContent == null)
    {
        await ctx.RespondAsync("Sorry, I didn't get a response!");
        return;
    }

    // Homework: have this change depending on if they say "good" or "bad", etc.
    await ctx.RespondAsync("Thank you for telling me how you are!");
} 
```

如果您想从命令中接受参数，您可以在 CommandContext 后面添加参数(例如:

`Interact(CommandContext ctx, int age, [remainder]string fullName);`

*   [remainder]只是说使用所有剩余的参数，并将它们放在字符串中。

好了，现在如果你运行这个机器人并输入/交互，它会问你好，并等待一分钟的回应！

## 结论

所以现在你已经记下了这一点，请随意查看 [DSharpPlus 文档](https://dsharpplus.emzi0767.com/)关于如何使用一些其他功能并为自己构建一个奇特的机器人！

让我知道你对此的看法。在我的机器人中，我实现了后台预定任务，一个支持多输出的日志记录器和一个提醒命令，当提醒到期时，它使用这些预定任务来提醒用户。

我还建立了带有评分系统、游戏、排行榜等的游戏机器人。所以，如果你想要一个如何做这些事情的指南，请在评论中告诉我，我可能会写一个。

希望你喜欢，一定要让我知道你创造了什么可怕的机器人！如果你对这篇文章有反馈也很好，这是我的第一篇 dev.to 文章！