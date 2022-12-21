# ASP.NET 核心的替代视图引擎:连接到 ASP.NET 核心架构

> 原文：<https://dev.to/dealeron/creating-an-alternative-view-engine-for-asp-net-core-3kln>

ASP.NET 核心是一个伟大的开源框架，用于构建 web 应用程序。它的优势之一是其模块化架构，这使得用替代组件替换许多默认组件成为可能。在本帖中，我们将看看如何使用 ASP.NET 创建和使用一个新的视图引擎。

## 创建剃刀视图

让我们首先创建一个带有剃刀视图的 ASP.NET 核心 MVC 应用程序。你需要[。网芯 SDK](https://dotnet.microsoft.com/download) 。在您的操作系统或您最喜欢的 IDE 中使用终端，如 [VS Code](https://code.visualstudio.com/) (一个很棒的跨平台 IDE)，创建一个名为 Foo 的项目，并用 MVC 应用程序的默认模板初始化它。

```
mkdir foo
cd foo/
dotnet new mvc 
```

现在，您可以使用`dotnet run`运行应用程序，并导航到`https://localhost:5001/`以访问显示欢迎消息的主页。

现在，我们将向主控制器添加一个名为 Bar 的动作以及一个相应的 Razor 视图。

编辑`HomeController.cs`以添加以下内容:

```
public IActionResult Bar(){
    ViewData["Message"] = "Hello World!";

    return View();
} 
```

创建`Views/Home/Bar.cshtml`并添加以下内容:

```
Your Message: @ViewData["Message"] 
```

如果您停止并再次运行该应用程序，您应该会在访问`https://localhost:5001/Home/Bar`时看到文本`Your Message: Hello World!`

## 替代视图引擎

正如我们上面看到的，ASP.NET 附带的 Razor 视图引擎使用扩展名为`.cshtml`的标记文件。Razor 语法使用`@`符号转换到 C#并计算表达式。作为替代，如果我们对标记文件使用 mustache 语法会怎么样？我们可以使用大括号来表示需要作为 C#表达式处理的变量。在我们的例子中，上面的工具条视图在新的模板系统中会是这样的:

```
Your Message: {{Message}} 
```

我们将使用与 Razor 类似的约定来定位视图——即`Views/[controller]/[action]`并使用文件扩展名`.stache`。让我们看看如何实现 Stache 视图引擎。

## 创建视图引擎

一个视图引擎需要实现两个接口:来自名称空间`Microsoft.AspNetCore.Mvc.ViewEngines`的`IViewEngine`和`IView`。在我们的项目中，创建一个名为`Stache`的顶级目录，包含文件`StacheViewEngine.cs`和`StacheView.cs`。我们将在下面介绍这两个接口的关键部分。你可以在这个 GitHub gist 上找到这两个文件的完整内容。

### IViewEngine

IViewEngine 需要实现两个方法:

```
public ViewEngineResult GetView (string executingFilePath, string viewPath, bool isMainPage);

public ViewEngineResult FindView (ActionContext context, string viewName, bool isMainPage); 
```

这两个方法都返回一个封装了`ViewEngineResult.Found`的`ViewEngineResult`，表示找到了视图的一个实例，还返回一个`ViewEngineResult.NotFound`，表示没有找到视图，并包含搜索位置的列表。

讨论从控制器返回的视图如何被引用，有助于理解这两种方法在呈现视图的过程中扮演的角色。在我们的 Bar 控制器中，我们只写了`return View();`，它引用了与动作同名的视图(即`Bar`)。我们也可以直接将视图命名为:`return View("Bar");`。或者，也可以明确地提供一个视图文件的完整路径:`return View("~/Views/Home/Bar.stache");`。在所有情况下，框架首先调用`GetView()`方法。如果没有找到视图，它将调用`FindView()`。

在视图路径被明确给出的情况下，我们期望`GetView()`返回该视图的一个实例。在我们只有视图名称的情况下，我们退回到`FindView()`来搜索各种路径，这些路径是我们按照惯例定义的视图可能存在的位置。

在我们对`GetView()`的实现中，我们首先检查`viewPath`是实际的文件路径还是仅仅是动作的名称。如果视图仅通过名称引用，我们将得到后者。在这种情况下，我们将返回`ViewEngineResult.NotFound`，这样呼叫可以进行到`FindView()`。

如果它是一个文件路径，我们将把它传递给一个私有方法`GetAbsolutePath()`以确保它的格式正确。`executingFilePath`只有在有另一个视图正在执行的情况下才会有值(例如，在处理 Razor 局部视图时的父视图)。如果它有一个值，它将与视图路径相结合，生成一个相对于父执行视图的路径。一旦我们有了路径，我们就检查该路径上是否存在模板，并适当地返回。

```
public ViewEngineResult GetView(string executingFilePath, string viewPath, bool isMainPage)
{
  if(string.IsNullOrEmpty(viewPath) || !viewPath.EndsWith(ViewExtension, StringComparison.OrdinalIgnoreCase)){
    return ViewEngineResult.NotFound(viewPath, Enumerable.Empty<string>());  
  }

  var appRelativePath = GetAbsolutePath(executingFilePath, viewPath);

  if(File.Exists(appRelativePath)){
    return ViewEngineResult.Found(viewPath, new StacheView(appRelativePath));
  }

  return ViewEngineResult.NotFound(viewPath, new List<string>{ appRelativePath});
} 
```

对于我们的`FindView()`实现，我们被提供了视图名称以及动作上下文，从中我们可以获得控制器名称。基于我们定位视图的惯例，我们将把这两个值插入到每个`_viewLocationFormats`(例如`Views/[controller]/[action].stache`)中，并检查模板文件，直到找到一个为止。如果没有找到，我们返回所有检查过的路径`ViewEngineResult.NotFound`。

```
public ViewEngineResult FindView(ActionContext context, string viewName, bool isMainPage)
{
  if(context.ActionDescriptor.RouteValues.TryGetValue("controller", out var controllerName)){
    var checkedLocations = new List<string>();

    foreach(var locationFormat in _viewLocationFormats){
      var possibleViewLocation = string.Format(locationFormat, viewName, controllerName);

      if(File.Exists(possibleViewLocation)){
        return ViewEngineResult.Found(viewName, new StacheView(possibleViewLocation));
      }
      checkedLocations.Add(possibleViewLocation);
    }

    return ViewEngineResult.NotFound(viewName, checkedLocations);
  }
  throw new Exception("Controller route value not found.");
} 
```

### IView

[IView](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.viewengines.iview?view=aspnetcore-2.2) 需要实现`Path`属性和`RenderAsync()`方法。`Path`是视图模板的位置。当在`GetView()`或`FindView()`中实例化视图时，我们填充`Path`属性。

`RenderAsync`方法负责在给定一个`ViewContext`的情况下呈现视图，这也是神奇的地方。在这个过于简单的例子中，我们只是在模板中寻找字符串`{{Message}}`，并用`ViewData`中提供的值替换它。在一个更加充实的版本中，这是您的自定义语法解析器完成工作的地方。

```
public Task RenderAsync(ViewContext context)
{
  var template = File.ReadAllText(Path);

  var processedOutput = template.Replace("{{Message}}", context.ViewData["Message"]?.ToString());

  return context.Writer.WriteAsync(processedOutput);
} 
```

## 使用替代视图引擎

让我们使用新的 Stache 模板语法创建一个替代的条形图视图。用内容:
创建文件`Views/Home/Bar.stache`

```
Your Message: {{Message}} 
```

最后一步是告诉 MVC 使用新的视图引擎。在`Startup.cs`中，将 MVC 配置修改为:

```
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
    .AddViewOptions(options => {
        options.ViewEngines.Insert(0, new StacheViewEngine());
    }); 
```

请注意，我们将 Stache 视图引擎添加为列表中的第一项。这将确保在给定视图中同时找到`.cshtml`和`.stache`标记文件时使用 Stache 模板。如果你想默认使用 Razor 视图，你可以把 Stache 添加到列表的末尾:`options.ViewEngines.Add(new StacheViewEngine());`。

如果您运行应用程序并导航到 Bar action `https://localhost:5001/Home/Bar`，您现在应该会看到使用 Stache 呈现的视图！

## 总结

在上面的例子中，我们看到插入一个替代视图引擎来代替 Razor 是相对简单的。它展示了在 ASP.NET 中使用多种标记模板的可能性，甚至可以同时使用。

虽然我们已经有了可以继续发展的基础，但是这个基本的例子非常有限:

*   我们只能绑定一个名为`Message`的变量。对于给定的模板，我们应该能够根据需要使用尽可能多的变量。
*   我们需要支持更高级的表达式，而不仅仅是变量(例如像`{{1 + 1}}`这样的内联表达式)，循环遍历列表，以及控制流的 if-else 条件。
*   我们遗漏了 Razor 的一些更高级的概念，比如布局、部分和标签助手。

探索这些缺失的特性并开发我们的替代视图引擎来实现它们将会很有趣。也许我们会在以后的文章中这样做。

*感谢 [@sohjsolwin](https://dev.to/sohjsolwin) 对这篇文章提供反馈。
还要特别感谢 Dave Paquette 的博客文章[“在 ASP.NET 核心中创建一个新的视图引擎”](https://www.davepaquette.com/archive/2016/11/22/creating-a-new-view-engine-in-asp-net-core.aspx)，感谢他整合了一些关键的难题。
封面照片由 [Eryk](https://unsplash.com/@eryk10) 通过[Unsplash](https://unsplash.com/)T11】*