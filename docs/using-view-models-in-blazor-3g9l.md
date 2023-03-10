# 在 Blazor 中使用视图模型

> 原文：<https://dev.to/pcmichaels/using-view-models-in-blazor-3g9l>

对于 Blazor(和 Razor)来说，第一件让我困惑的事情是这个视图似乎与应用程序的其他部分分离了。事实上，这实际上是一个非常好的设计，因为它强制使用了 DI。

例如，假设您想要为您视图创建一个视图模型，您可以在启动时注册这个视图模型:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<MyViewModel, MyViewModel>();
} 
```

注意这里你不需要接口。如果你只是为了这个目的而创建一个接口，那么这个抽象就没有任何好处。这并不是说没有理由使用接口，但是如果你有一个接口并且这是唯一使用它的地方，你可能应该重新考虑。

Razor / Blazor 中的视图(在撰写本文时)是*。剃刀锉。为了解决视图内部的依赖关系，可以使用以下语法:

```
@page "/"
@inject ViewModels.MyViewModel MyViewModel 
```

(注意@ page“/”在这个代码片段中只是为了给代码定位。)

您可以在视图模型中调用初始化，如下所示:

```
@code {

    protected override async Task OnInitAsync()
    {
        await MyViewModel.Init();
    }    
} 
```

并且，在您的 HTML 中，您可以像这样引用视图模型:

```
<div>@MyViewModel.MyData</div> 
```

魔法。希望布拉索很快会有更多的消息。

# 参考

这篇文章的原始版本可以在这里找到[。](https://www.pmichaels.net/2019/07/07/using-view-models-in-blazor/)