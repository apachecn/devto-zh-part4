# 解决布尔身份危机:第 3 部分

> 原文：<https://dev.to/elpapapollo/solving-the-boolean-identity-crisis-part-3-2f8>

*最初发表于[programming-elm.com](https://programming-elm.com/blog/2019-06-17-solving-the-boolean-identity-crisis-part-3)T3。*

* * *

在[的上一篇文章](https://dev.to/elpapapollo/solving-the-boolean-identity-crisis-part-2-12k)中，你学习了布尔返回值是如何导致布尔盲的。布尔盲会让代码访问不应该可用的数据，从而在条件语句中产生错误。我们用`Maybe`和一个自定义类型替换了布尔返回值，将数据包装在一个特定的类型构造函数中，并提供更有意义的返回值。这使得编译器可以确保代码只访问真正可用的数据。

在本文中，您将看到 Elm 记录中的布尔属性为无效的状态配置打开了大门。布尔属性需要更复杂的条件代码和更多的测试来防止错误。您将了解到自定义类型——在这里看到一种模式——通过利用编译器的能力来防止无效的状态配置，消除了对复杂代码和测试的需要。

## 问题

我以我在用 [Redux](https://redux.js.org) 和 [React](https://reactjs.org) 构建应用程序时遇到的一个问题开始我的演讲[解决布尔身份危机](https://www.youtube.com/watch?v=8Af1bh-BVY8)。当从服务器获取数据时，我会用多个布尔属性跟踪获取数据的状态。不幸的是，我将这种模式带到了我构建的 Elm 应用程序中。

例如，假设我们正在构建一个跟踪搜救犬的应用程序。我们需要从服务器上获取一只狗。最初，我们不会养狗，所以我们可能会有一个这样的模型。

```
type alias Model =
    { dog : Maybe Dog } 
```

到目前为止，这似乎是合理的。接下来，我们想在从服务器获取狗的时候显示一个加载微调器。因此，我们可以向模型添加一个`fetching`属性。

```
type alias Model =
    { dog : Maybe Dog
    , fetching : Bool
    } 
```

当`fetching`为`True`时，我们将显示微调器。当`fetching`为`False`时，我们将不显示任何内容。

一旦我们有了狗，抓取应该是`False`，但是我们想展示狗。我们可以添加一个`success`布尔属性来表示我们有狗。

```
type alias Model =
    { dog : Maybe Dog
    , fetching : Bool
    , success : Bool
    } 
```

现在，如果成功是`True`，我们展示这只狗。否则，如果它是`False`，获取是`False`，那么我们回到“准备获取”状态，应该什么也不显示。(或者，我们可以查看`fetching`和`dog`是否为`Just`或`Nothing`的组合来决定我们处于什么状态。)

一切看起来都很好，但是狗不能存在于服务器上，否则我们可能会遇到其他服务器错误。我们需要知道请求是否失败，并适当地处理任何错误。嗯，我们可以添加一个`error`布尔属性和一个`errorMessage`属性。

```
type alias Model =
    { dog : Maybe Dog
    , fetching : Bool
    , success : Bool
    , error : Bool
    , errorMessage : String
    } 
```

如果错误是`True`，那么我们可以显示`errorMessage`。否则，我们需要检查其他布尔属性来决定该做什么。

如果我们在`view`函数中处理它，它可能看起来像这样。

```
view : Model -> Html Msg
view model =
    if model.error then
        viewError model.errorMessage

    else if model.fetching then
        viewSpinner

    else if model.success then
        viewDog model.dog

    else
        viewSearchForm 
```

`view`函数有几个问题。

1.  它患有[布尔盲](https://dev.to/blog/2019-05-30-solving-the-boolean-identity-crisis-part-2)。在试图访问数据之前，我们依赖于某些布尔属性为真。没有什么可以阻止我们访问其他分支中的数据，比如`model.dog`或`model.errorMessage`。(当然，如果我们试图访问`model.dog`，我们仍然有`Maybe`的安全性。)
2.  它需要更彻底的自动化测试，以确保我们妥善处理所有案件。我们可以省去所有的`else if`分支，即使代码不正确，它仍然可以编译。

此外，我们的模型可能会得出这样不正确的配置。

```
{ dog = Just { name = "Tucker" }
, fetching = True
, success = True
, error = True
, errorMessage = "Uh oh!"
} 
```

所有布尔属性为真，我们有一只狗，我们有一个`errorMessage`。我们很难确定自己到底处于什么状态。我们别无选择，只能依靠`view`中 if-else 条件的任意顺序来做出决定。当然，我们需要一个强大的测试套件来确保我们不能像这样配置模型。

## 防止无效状态

我终于意识到我如何表示我的数据的问题。我认为获取数据的状态(就绪、获取、成功和错误)是相互独立的。实际上，它们是同一整体状态的不同状态值。那听起来像是一个[国家机器](https://en.wikipedia.org/wiki/Finite-state_machine)。

一个状态机一次只能有一个状态值。我的记录表示禁止让多个状态值为`True`。Elm 有一个很棒的类型系统和编译器。我们应该尽可能地利用它们，通过创建一个状态机来防止无效的状态配置。

我们可以引入新的自定义类型。

```
type RemoteDoggo
    = Ready
    | Fetching
    | Success Dog
    | Error String 
```

`RemoteDoggo`类型有四个构造函数，映射到每个可能的状态，`Ready`、`Fetching`、`Success`和`Error`。我们用`Success`构造函数包装一个`Dog`，用`Error`构造函数包装一个`String`错误消息。然后，我们可以更新模型，使其看起来像这样。

```
type alias Model =
    { dog : RemoteDoggo } 
```

我们移除除了`dog`属性之外的所有属性，并将`dog`属性更改为`RemoteDoggo`类型。我们现在可以将`view`函数转换成这样。

```
view model =
    case model.dog of
        Ready ->
            viewSearchForm

        Fetching ->
            viewSpinner

        Success dog ->
            viewDog dog

        Error error ->
            viewError error 
```

我们不用担心布尔属性的顺序，而是用一个`case`表达式对`dog`属性进行模式匹配。我们将每个构造函数映射到适当的视图助手函数。

通过这种改变，我们的代码获得了一些好处。

1.  代码用`RemoteDoggo`类型使状态显式化。
2.  我们消除了布尔盲。我们只能访问`Success`中的狗和`Error`中的错误信息。
3.  我们有编译器强制的 UI 状态。如果我们忘记处理其中一个`RemoteDoggo`值，那么我们的代码将无法编译。

此代码:

```
 view model =
     case model.dog of
         Ready ->
             viewSearchForm

         Fetching ->
             viewSpinner

         Success dog ->
             viewDog dog

         -- forgetting to handle errors 
```

会产生这个编译错误:

```
 This `case` does not have branches for all possibilities:

 |>    case model.dog of
 |>        Ready ->
 |>            viewSearchForm
 |>
 |>        Fetching ->
 |>            viewSpinner
 |>
 |>        Success dog ->
 |>            viewDog dog

 Missing possibilities include:

     Error _ 
```

由于自定义类型，现在我们的代码更加清晰和安全。

## 你学到了什么

在这篇文章中，您了解了布尔属性会导致无效的状态配置，这会产生编译器无法捕捉的错误。布尔属性会导致复杂的 if-else 表达式，这些表达式具有难以理解的任意顺序。您已经看到，通过将布尔属性减少到一个自定义类型的属性，您可以编写更显式的代码，并且具有编译器安全性。如果您的一些代码具有类似这样的多个布尔属性，请尝试重构为自定义类型，以使您的代码更清晰、更安全。

## 进一步资源

关于如何使用 Elm 的类型系统来防止无效状态配置的更多信息，请观看 Richard Feldman 的演讲[让不可能的状态变得不可能](https://www.youtube.com/watch?v=IcgmSRJHu_8)。

对于类似于`RemoteDoggo`的更一般的类型，检查一下 [krisajenkins/remotedata](https://package.elm-lang.org/packages/krisajenkins/remotedata/latest) 包。

要了解更多关于如何有效构建 Elm 应用程序的信息，请从[实用程序员](https://pragprog.com/book/jfelm/programming-elm)那里拿一本我的书[Elm 编程](https://programming-elm.com)。