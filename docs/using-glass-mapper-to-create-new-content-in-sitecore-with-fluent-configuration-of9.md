# 使用 Glass Mapper 通过 Fluent 配置在 Sitecore 中创建新内容

> 原文：<https://dev.to/kmac23va/using-glass-mapper-to-create-new-content-in-sitecore-with-fluent-configuration-of9>

我发现自己今天在 StackExchange 上问并回答了自己的问题(顺便说一下，如果你找到了一个好的答案，不要害怕这样做，也要准备好更好的答案来回答可能出现的问题！):

<header>![](img/70920991d6111d21097e8fcc58a16302.png) [Using Glass to add content with fluent config - map to interface and concrete class](https://sitecore.stackexchange.com/questions/20624/using-glass-to-add-content-with-fluent-config-map-to-interface-and-concrete-cl) Aug 22 '19 Comments: 1 Answers: 1[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)0![](img/fd423aaf5fec73c645f97544689ea934.png)](https://sitecore.stackexchange.com/questions/20624/using-glass-to-add-content-with-fluent-config-map-to-interface-and-concrete-cl) </header>

我对 Glass 和 fluent config 的典型构建是使用接口和映射文件(使用 SitecoreGlassMap)进行建模，但我知道如果我需要使用模型向 Sitecore 添加数据，我不能使用接口类型。从最佳实践的角度来看，如果我的模型是…

[Open Full Question](https://sitecore.stackexchange.com/questions/20624/using-glass-to-add-content-with-fluent-config-map-to-interface-and-concrete-cl)

作为开场总结，我在 Sitecore 上使用 Glass 的典型设置是使用 fluent 配置。所以如果我有一个这样的模型:

```
public interface ITemplate {
  string Field { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我将创建这个映射条目，将 POCO 链接到 Sitecore:

```
public class TemplateMap : SitecoreGlassMap<ITemplate> {
  public override void Configure() =>
    Map(config => {
      config.Field(f => f.Field).FieldName("Field");
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以用类似下面的代码检索该项目(注意，`SitecoreService`需要替换为建立服务的适当安排的调用):

```
ITemplate item = SitecoreService.GetItem<ITemplate>(new GetItemByIdOptions(Guid.Parse("{}"))); 
```

Enter fullscreen mode Exit fullscreen mode

所以这一切都很好。但是当你出于任何原因需要向 Sitecore 添加内容时，你不能使用接口，而是需要一个标准类:

```
public class Template : ITemplate {
  public string Field { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您已经完成了玻璃训练，您将在创建项目一节中看到它们使用属性映射。这很简单，但是如何使用流畅的配置呢？现在，地图只显示在界面上。那么我们是必须使用属性映射，还是单独做一张地图呢？

事实证明这很简单。您所要做的就是更改地图以使用具体的类:

```
public class TemplateMap : SitecoreGlassMap<Template> {
  public override void Configure() =>
    Map(config => {
      config.Field(f => f.Field).FieldName("Field");
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您使用前面的`GetItem`调用，它仍然可以工作。然后如果你需要创建一个新的条目作为它的子项，这段代码将会工作:

```
ITemplate newItem = new Template {
  Name = "Test Item"
};

using (new SecurityDisabler()) {
  ITemplate createOrg = SitecoreService.CreateItem<ITemplate>(new CreateByModelOptions {
    Model = newItem,
    Parent = item
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

所以只需要一个额外的类并改变映射调用中使用的类，就可以了！我个人建议，当你必须做这些操作时，只引入具体的类，因为如果你只是做读操作，接口会工作得非常好。