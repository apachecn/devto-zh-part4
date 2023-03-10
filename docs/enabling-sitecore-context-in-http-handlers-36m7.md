# 在 HTTP 处理程序中启用 Sitecore 上下文

> 原文：<https://dev.to/kmac23va/enabling-sitecore-context-in-http-handlers-36m7>

我正在做的一个项目是从一个定制的 ColdFusion 系统迁移到 Sitecore。客户端通过一个带有 ID 参数的 **index.cfm** 文件路由所有链接，该 ID 参数有一个公式来获取 SQL 主键。所以我需要拦截对 index.cfm 的调用，并适当地路由它们。幸运的是，有一种方法可以在 web.config 中使用处理程序做到这一点:

```
 <system.webServer>
        <handlers>
            <add verb="*" path="index.cfm" type="Foundation.Core.Handlers.LegacyItemResolver, Foundation.Core" name="Foundation.Core.Handlers.LegacyItemResolver" xdt:Locator="Match(path)" xdt:Transform="InsertIfMissing" />
        </handlers>
    </system.webServer> 
```

Enter fullscreen mode Exit fullscreen mode

然而，有一个问题...如果你想对 Sitecore 上下文做任何事情，你会发现它是空的。基本上，我们在这个过程中还为时过早，无法了解背景。然而，如果您查看处理程序，您会看到一个 MediaRequestHandler，如果您使用 dotPeek 来检查引用，*是否使用了 Sitecore 上下文。那么这是怎么回事呢？我不得不四处打听...*

<header>![](img/70920991d6111d21097e8fcc58a16302.png) [Sitecore Context null in web.config handler](https://sitecore.stackexchange.com/questions/20600/sitecore-context-null-in-web-config-handler) Aug 20 '19 Comments: 3 Answers: 2[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)1![](img/fd423aaf5fec73c645f97544689ea934.png)](https://sitecore.stackexchange.com/questions/20600/sitecore-context-null-in-web-config-handler) </header>

当我们从旧的 ColdFusion 系统迁移到 Sitecore 时，我试图创建一个处理程序来拦截 index.cfm 文件。我已经指定了这样的处理程序:

```
<system.webServer>
    <handlers>
        <add verb="*" path="index.cfm" type="Foundation.Core.Pipelines.LegacyItemResolver, Foundation.Core" name="Foundation.Core.Pipelines.LegacyItemResolver" xdt:Locator="Match(path)" xdt:Transform="InsertIfMissing" />
    </handlers>
</system.webServer> 
```

然后我的类定义是这样的，处理建模 I…

[Open Full Question](https://sitecore.stackexchange.com/questions/20600/sitecore-context-null-in-web-config-handler)

为了总结 JammyKam 的回答，我必须在 Sitecore 补丁文件中添加一个自定义处理程序条目:

```
<configuration xmlns:set="http://www.sitecore.net/xmlconfig/set/">
    <sitecore>
        <customHandlers>
            <handler trigger="index.cfm" handler="cfm_redirect.ashx"/>
        </customHandlers>
    </sitecore>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

然后在 web.config 条目中，不是将 **path** 属性设置为 index.cfm，而是设置为 **cfm_redirect.ashx** 。

现在在我的 LegacyItemResolver 中，这段代码将显示适当的数据:

```
namespace Foundation.Core.Handlers {
    public class LegacyItemResolver : IHttpHandler, System.Web.SessionState.IRequiresSessionState {
        public void ProcessRequest(HttpContext context) {
            string database = Sitecore.Context.Database.Name;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode