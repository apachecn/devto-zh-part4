# 枚举和 API

> 原文：<https://dev.to/timothymcgrath/enums-apis-15n4>

枚举是一把双刃剑。它们对于创建一组可能的值非常有用，但是如果您向该枚举添加一个值，它们可能会成为一个版本问题。

在完美的世界中，一个枚举代表一个封闭的值集，所以版本控制从来都不是问题，因为你从来没有给一个枚举添加一个值。然而，我们生活在真实的、不完美的世界中，看似封闭的价值观往往是开放的。

所以，让我们开始吧。

### 啤酒 API

我的示例 API 是一个啤酒 API！

我有一个返回啤酒的 GET 和一个接受啤酒的 POST。

```
[HttpGet]
public ActionResult<Models.Beer> GetBeer()
{
    return new ActionResult<Models.Beer>(new Models.Beer()
    {
        Name = "Hop Drop",
        PourType = Beer.Common.PourType.Draft
    });
}

[HttpPost]
public ActionResult PostBeer(Models.Beer beer)
{
    return Ok();
} 
```

啤酒类:

```
public class Beer
{
    public string Name { get; set; }

    public PourType PourType { get; set; }

} 
```

和 PourType 枚举:

```
public enum PourType
{
    Draft = 1,
    Bottle = 2
} 
```

API 还将所有枚举转换为字符串，而不是整数，这是我推荐的最佳实践。

```
 services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
                .AddJsonOptions(options =>
                {
                    options.SerializerSettings.Converters.Add(new Newtonsoft.Json.Converters.StringEnumConverter());
                }); 
```

所以，最大的问题在于啤酒类中的 PourType 的定义。

```
public PourType PourType { get; set; } 
```

应该是这个代替吗？

```
public string PourType { get; set; } 
```

我们将通过考虑如果我们向 PourType 添加一个新值 Can = 3 会发生什么来研究这个问题。

我们来看看利弊。

### 定义为枚举

#### 优点

当您将 PourType 定义为 Beer 上的一个枚举时，默认情况下会创建可发现性和验证。当您添加 Swagger 时(正如您应该做的那样)，它将 PourType 的可能值定义为 API 的一部分。更好的是，当您根据 Swagger 生成客户端代码时，它会在客户端定义 Enum，因此它们可以很容易地向您发送正确的值。

#### 弊

向后兼容性现在是一个问题。当我们将 Can 添加到 PourType 中时，我们已经创建了一个客户机不知道的新值。因此，如果客户端请求啤酒，而我们返回的啤酒类型为 Can，那么在反序列化时就会出错。

### 定义为字符串

#### 优点

就反序列化而言，这允许新值向后兼容客户端。这在客户端实际上不关心值或者客户端从不将它用作枚举的情况下非常有用。

然而，从 API 的角度来看，您不知道这是真是假。这很容易导致运行时错误。如果客户端试图将其转换为枚举，将会出错。如果客户端使用 If 或 SWITCH 语句中的值，将会导致意外的行为和可能的错误。

#### 弊

最大的问题是可发现性消失了。客户端不知道可能的值集是什么，它必须传递一个字符串，但不知道是什么字符串。

这可以用文档来处理，但是众所周知，文档已经过时了，在 API 上定义文档对于客户来说是一个容易得多的过程。

### 那么我们该怎么办？

这是我决定的。

## 枚举！

API 应该尽可能完整地描述自己，包括枚举值的可能值。没有这些值，客户端不知道可能的值是什么。

因此，新的枚举应该被视为 API 的版本变化。

有几种方法可以处理这种版本变化。

#### 滤镜

V1 控制器现在可以过滤啤酒列表，删除任何具有罐装啤酒类型的啤酒。如果啤酒只有在客户能够理解啤酒类型的情况下才有意义，这可能是没问题的。

#### 未知值

Filter 方法在某些情况下会起作用，但在其他情况下，您可能仍然希望返回结果，因为该枚举值不是资源的关键部分。

在这种情况下，请确保您的枚举具有未知值。它将需要在 V1 的工作。当 V1 控制器获得罐装啤酒时，它可以将其更改为未知。

以下是 PourType 的枚举:

```
public enum PourType
{
    /// <summary>
    /// Represents an undefined PourType, could be a new PourType that is not yet supported.
    /// </summary>
    Unknown = 0,
    Draft = 1,
    Bottle = 2
} 
```

因为 V1 API 合同中列出了“未知”,所以所有客户都应该预料到“未知”的可能性并处理它。客户端可以决定如何处理这种情况...它可能没有影响，它可能有一个 UI 来显示特定功能不可用，或者它可以选择出错。重要的是客户应该已经预料到这种可能性。

### 资源方案

在这种情况下，应该考虑的一件事是 enum 实际上是一种资源。

PourType 是一套价值观，随着更多喝啤酒的方式被发明出来，这种价值观可能会扩大(万岁！).从 API 中公开 PourType 值的列表可能更有意义。这可以防止 PourType 添加新值时发生任何版本更改。

当客户端只关心值的列表时(例如，在 combobox 中显示值)，这种方法非常有效。但是，如果客户端需要基于值编写逻辑，它仍然会遇到新值的问题，因为它们将在默认情况下着陆。

将枚举公开为资源还允许将附加行为添加到值中，这有助于客户端逻辑。例如，我们可以为 RequiresBottleOpener 的 PourType 添加一个属性，这样客户端就可以不依赖于“Bottle”值而只依赖于 RequiresBottleOpener 属性来做出逻辑决策。

PourType 资源定义:

```
public class PourType
{
    public string Name { get; set; }

    public bool RequiresBottleOpener {  get; set; }
} 
```

PourType 控制器:

```
[HttpGet]
public ActionResult<IEnumerable<PourType>> GetPourTypes()
{
    // In real life, store these values in a database.
    return new ActionResult<IEnumerable<PourType>>(
        new List<PourType>{
                new PourType {Name = "Draft"},
                new PourType {Name = "Bottle", RequiresBottleOpener = true},
                new PourType {Name = "Can"}
        });
} 
```

然而，这种方法确实增加了 API 和客户端的复杂性，所以我不建议对每个 enum 都这样做。当您有一个随着时间推移将具有额外值的枚举的明确案例时，请使用资源方法。

### 结论

我花了很多时间思考这个问题，我相信这是满足我特定需求的最佳途径。

如果你用不同的方式解决了这个问题，请在评论中讨论。我不相信有一个完美的解决方案，所以看看其他人的解决方案会很有趣。