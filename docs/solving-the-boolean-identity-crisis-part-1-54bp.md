# 解决布尔身份危机:第 1 部分

> 原文：<https://dev.to/elpapapollo/solving-the-boolean-identity-crisis-part-1-54bp>

*最初发表于[programming-elm.com](https://programming-elm.com/blog/2019-05-20-solving-the-boolean-identity-crisis-part-1)T3。*

* * *

回到 2017 年 9 月，我在 ElmConf 发表了“解决布尔身份危机”的演讲。这篇演讲强调了在 Elm 代码中使用布尔值的缺点，并提供了编写更清晰、更易维护的代码的方法。这篇文章和接下来的几篇文章将分享我在那次演讲中探索的东西。你可以通过在 YouTube 上观看我的演讲来预览未来。

在这篇文章中，你将会看到布尔函数参数是如何模糊代码意图的。然后，您将学习如何用 Elm 的自定义类型替换布尔参数，以编写更容易理解的代码。

## 问题

看看这个函数调用，理解布尔参数的问题。

```
bookFlight "OGG" True 
```

我们将一个字符串参数`"OGG"`和一个布尔参数`True`传递给一个`bookFlight`函数。如果您在 Elm 代码库中遇到这种情况，您可能想知道布尔参数是做什么的。

布尔参数隐藏了代码的意图。不查`bookFlight`的定义，我们不知道这里`True`值的意义。布尔参数使得这段代码更难理解，特别是对于一个学习代码库的新手来说。

查找定义，我们发现这个。(我使用`...`来表示不相关的代码。)

```
bookFlight : String -> Bool -> Cmd Msg
bookFlight airport isPremium =
    if isPremium then
        ...

    else
        ... 
```

布尔参数称为`isPremium`，因此它意味着预订客户具有高级状态。我们使用 if-else 表达式在`isPremium`上进行分支。如果`isPremium`是`False`，我们不确定这个客户是什么身份。我们必须假设客户具有“常规”状态，因为代码暗示了这一点。由于使用了布尔参数，我们已经失去了这段代码的明确意图。

如果我们需要一个以上的客户状态，这个代码将会出现问题。例如，假设我们需要引入一个新的经济状态。我们可以引入另一个名为`isRegular`的布尔参数。

```
bookFlight : String -> Bool -> Bool -> Cmd Msg
bookFlight airport isPremium isRegular =
    if isPremium then
        ...

    else if isRegular then
        ...

    else
        ... 
```

检查完`isPremium`是否为`True`后，我们检查`isRegular`是否为`True`。否则，隐含的客户身份就是经济。

现在，函数调用会像这样。

```
bookFlight "OGG" True False 
```

那就更混乱了。我们也很容易混淆布尔参数的顺序，并意外地以错误的状态预订客户。此外，我们可以很容易地传入两个`True`参数。客户不能同时拥有高级和普通状态。我们必须让第一个布尔参数`isPremium`在 if-else 表达式中优先处理这个无效的参数排列。

## 表明意图

我们可以通过用 Elm 自定义类型替换布尔参数来清理`bookFlight`函数。与其将状态隐藏在布尔值后面，不如让它们显式化。我们可以很容易地将每种状态编码成这样。

```
type CustomerStatus
    = Premium
    | Regular
    | Economy 
```

我们添加了一个带有三个值的定制类型，或者说构造函数。每个值完美地编码了每个状态、`Premium`、`Regular`和`Economy`。

我们可以这样更新`bookFlight`函数。

```
bookFlight : String -> CustomerStatus -> Cmd Msg
bookFlight airport customerStatus =
    case customerStatus of
        Premium ->
            ...

        Regular ->
            ...

        Economy ->
            ... 
```

`bookFlight`函数清楚地说明了如何在没有隐含 if-else 分支的情况下处理每个客户状态。此外，编译器确保我们处理每个状态。在先前版本的带有两个布尔参数的`bookFlight`中，没有什么可以阻止我们意外忘记处理`else if isRegular`分支。编译器会接受这段代码。

```
bookFlight airport isPremium isRegular =
    if isPremium then
        ...

    else
        ... 
```

如果我们忘记了类型为`CustomerStatus`的版本中的`Regular`分支，代码将无法编译。

此代码:

```
bookFlight airport customerStatus =
    case customerStatus of
        Premium ->
            ...

        Economy ->
            ... 
```

会导致以下编译器错误:

```
This `case` does not have branches for all possibilities:

|>    case customerStatus of
|>        Premium ->
|>            ...
|>
|>        Economy ->
|>            ...

Missing possibilities include:

    Regular 
```

自定义类型为显式代码提供了编译器安全性。现在对`bookFlight`的调用声明了代码的意图，因为我们直接传入了`CustomerStatus`。

```
bookFlight "OGG" Premium 
```

如果我们碰到上面的函数，我们会更容易理解发生了什么。我们正在为一位高级客户预订航班。我们已经使代码更清晰，更易于维护。

## 你学到了什么

在这篇文章中，您了解了布尔参数如何通过隐藏代码的意图使代码变得混乱和不可维护。您看到了用自定义类型值替换布尔参数如何创建更好、更安全的代码。在您自己的 Elm 项目中尝试这种技术。找到一个接受布尔参数的函数，看看是否可以定制一个类型，当它是`True`和`False`时，更明确地编码布尔参数的含义。

要了解更多关于如何有效构建 Elm 应用程序的信息，请从[实用程序员](https://pragprog.com/book/jfelm/programming-elm)那里拿一本我的书[Elm 编程](https://programming-elm.com)。