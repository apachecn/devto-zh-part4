# 安全。带特征切换的网络特征标志

> 原文：<https://dev.to/integerman/safe-net-feature-flags-with-featuretoggle-450h>

昨天[我在一篇基本上语言中立的综述文章中写了关于特性标志](https://dev.to/integerman/fun-with-feature-flags-31jf)的内容。

今天我想讨论一下我为。NET 应用: [FeatureToggle](https://github.com/jason-roberts/FeatureToggle) 作者杰森·罗伯茨。

功能切换是一个非常小的专用库，致力于各种灵活的功能切换变化。

在 FeatureToggle 中，您将一个可切换特性定义为一个类，该类从 Feature Toggle 类(如`SimpleFeatureToggle`或`IFeatureToggle`接口)扩展而来。

一个简单的特性定义应该是这样的:

```
public class UseNewAnalyzer : SimpleFeatureToggle
{

} 
```

Enter fullscreen mode Exit fullscreen mode

然后可以这样检查该特性:

```
private static IResumeAnalyzer GetResumeAnalyzer()
{
    var useNewAnalyzer = new UseNewAnalyzer();
    if (useNewAnalyzer.FeatureEnabled)
    {
        return new RewrittenAnalyzer();
    }
    else
    {
        return new ResumeAnalyzer();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将在应用程序的配置文件中寻找一个条目(`appSettings.json`、`web.config`或`app.config`，这取决于应用程序运行的类型)，并将该值解析为一个布尔值，以确定`FeatureEnabled`返回 true 还是 false。

一个样本。这里列出了带有特征标志的. NET Core `appSettings.json`文件:

```
{  "FeatureToggle":  {  "UseNewAnalyzer":  "true"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

# 功能切换的设计

空的/ marker 类并实例化一个对象，只是为了检查它的一个属性并丢弃它？这怎么可能是好事？

事实证明，代码看起来像这样的原因是杰森·罗伯茨有意设计了 FeatureToggle，使它难以犯与功能标志相关的常见错误。

因为如果与切换相关联的配置设置不存在，FeatureToggles 将抛出异常，所以这有助于您捕捉坏的应用程序配置，而不是因为配置更改从未移植到新环境而意外地使功能脱机。

此外，由于 FeatureToggle 使用类名来查找配置设置，因此代码中不存在可能会导致检查错误配置设置的字符串。

这降低了其他特性标志库常见的一些风险，增加的安全性有助于提高代码质量，并防止特定于环境的错误。

# 清理样本代码

如果上面的代码给你带来了问题，可以通过使用如下的静态属性来简化:

```
public class UseNewAnalyzer : SimpleFeatureToggle
{
    private static readonly UseNewAnalyzer Instance = new UseNewAnalyzer();

    public static bool IsFeatureEnabled => Instance.FeatureEnabled;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
private static IResumeAnalyzer GetResumeAnalyzer()
{
    if (UseNewAnalyzer.IsFeatureEnabled)
    {
        return new RewrittenAnalyzer();
    }
    else
    {
        return new ResumeAnalyzer();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用静态 getter `IsFeatureEnabled`，调用代码不再需要创建特性类的实例，代码读起来简单多了。此外，由于静态属性和实例字段，您不再有空类。

# 不同类型的特性切换

FeatureToggle 也设计得很灵活，通过子类化或使用`IFeatureToggle`接口来提供可扩展性。

例如，我可以通过编写以下代码来编写一个只在周末活动的实现:

```
public class OnlyOnWeekends : IFeatureToggle
{
    public bool FeatureEnabled
    {
        get
        {
            switch (DateTime.Today.DayOfWeek)
            {
                case DayOfWeek.Saturday:
                case DayOfWeek.Sunday:
                    return true;

                default:
                    return false;
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我不确定你为什么想要这种类型的特性切换，但是如果你可以使用这个框架来创建像这样简单的东西，它肯定可以用来创建更高级的东西。

好消息是你不需要创造更先进的东西。特性 Toggle 附带了许多内置实现:

*   到目前为止我们一直使用的例子。它从配置文件中读取一个值。
*   **enabledonoraftatefeaturetoggle**-只有到了日期才可用的功能。这可以用作“上线”日期。
*   **EnabledOnOrBeforeDateFeatureToggle**-该功能仅在日期到来之前可用。这可以作为退休日期。
*   **EnabledBetweenDatesFeatureToggle**-仅在两个日期之间可用的功能
*   **EnabledOnDaysOfWeekFeatureToggle**-该功能仅在一周中的特定日期可用。
*   **AlwaysOnFeatureToggle** -指定一个无论如何都将被激活的功能。这可以用来从配置管理中迁移出来，而不需要完全删除类。
*   **AlwaysOffFeatureToggle**——类似于`AlwasOnFeatureToggle`，但是*永远不会*被激活。
*   **HttpJsonFeatureToggle**——这向特性的配置文件中配置的位置发出 HTTP GET 请求，并期待类似于`{"enabled": true}`的结果，然后解析该`enabled`值以确定该特性是否被启用。请记住，这将是一个同步 web 调用，由于网络、互联网和防火墙问题，很容易出错。
*   **SqlFeatureToggle**——使用它，您可以在配置中定义一个连接字符串以及一个返回单个布尔值的 SQL 查询。然后，这个值就是该特性是否启用的返回值。同样，这是一个同步操作，比许多其他实现更容易出错。
*   **enabledonoaraftassemblyversionwheretogglesdefinedtowgle**-对照配置文件中定义的版本检查当前的程序集版本，如果配置的版本已经到达，则激活该功能。这可用于规划在不知道发布日期的情况下与主要发布号一起发布的特性

FeatureToggle 显然非常适合处理最基本的需求，并且足够简单和可扩展，能够适应您遇到的任何定制需求。

* * *

如果你想了解更多关于 FeatureToggle 的知识，请查看 GitHub 库并尝试一下这个库。

如果你想要更多的学习材料，观看杰森·罗伯特的精彩课程。这就是我第一次发现这个图书馆的原因，看着一位教师讲授他们创建的图书馆非常有趣。

* * *

Kari Shea 在 Unsplash 上拍摄的封面照片