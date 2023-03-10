# 解决布尔身份危机:第二部分

> 原文：<https://dev.to/elpapapollo/solving-the-boolean-identity-crisis-part-2-12k>

*最初发表于[programming-elm.com](https://programming-elm.com/blog/2019-05-30-solving-the-boolean-identity-crisis-part-2)T3。*

* * *

在[之前的文章](https://dev.to/elpapapollo/solving-the-boolean-identity-crisis-part-1-54bp)中，我们探讨了布尔参数是如何掩盖代码意图的。我们用自定义类型值替换了布尔参数，以使代码更加明确和易于维护。

在这篇文章中，你会发现布尔返回值会导致一个被称为*布尔盲*的问题。布尔盲会在 if-else 表达式中产生编译器无法防止的意外错误。您将学习如何用自定义类型替换布尔返回值，以消除布尔盲并利用编译器获得更安全的代码。

## 问题

在我的演讲“解决布尔身份危机”中，我分享了一个来自卫斯理大学教授丹·利卡塔的 T2 讲座的故事。

> 有时，当我走在街上时，有人会问我“你知道现在几点了吗？”如果我想成为一个字面主义者，我会说“是”然后他们翻着白眼说:“好吧，告诉我现在几点了！”这样做的坏处是，他们可能会习惯于要求时间，并开始要求那些甚至不知道时间的人。不如问“你知道现在几点了吗，如果知道，请告诉我？”。[T]这就是“几点了？”通常意味着。这样，当信息可用时，您就可以得到您想要的信息。

如果我们把它翻译成代码，它可能看起来像这样。

```
type alias Person =
    { time : String }

doYouKnowTheTime : Person -> Boolean
doYouKnowTheTime person =
    person.time /= ""

tellMeTheTime : Person -> String
tellMeTheTime person =
    person.time

currentTime : Person -> String
currentTime person =
    if doYouKnowTheTime person then
        tellMeTheTime person

    else
        "Does anybody really know what time it is?" 
```

`doYouKnowTheTime`函数接受一个`Person`类型并检查`time`字段是否不是空字符串。然后，我们分支调用`currentTime`函数中的`doYouKnowTheTime`。如果它返回`True`，那么我们调用`tellMeTheTime`返回`person.time`的值。否则，我们返回一个默认时间。

这段代码看起来不错，但是它有几个问题。

首先，正如丹正确指出的那样，人们可以向没有时间的人索取时间。没有什么能阻止我们编写这段代码。

```
currentTime person =
    if doYouKnowTheTime person then
        tellMeTheTime person

    else
        tellMeTheTime person -- returns empty string 
```

当`person.time`为空字符串时，我们仍然可以调用`tellMeTheTime`。这可能会导致一个错误。

第二，我们可能导致前一种情况的事实暴露了数据建模代码的味道。众所周知，字符串会带来麻烦，因为根据类型系统，任何字符串都是有效的。编译器不能强制给定的字符串不为空。对于更有意义的数据类型来说，这是一个很弱的替代品。

我们想给编译器更好的类型信息，这样它就可以约束代码只在真正可用的时候访问。让我们来探讨一下如何让这段代码更清晰、更安全。

## 修复布尔盲症

第一个问题源于布尔盲。当您将信息简化为布尔值时，您很容易丢失该信息。布尔运算携带的信息只有在`if`检查中才能知道。一旦你分支进入 if-else 表达式的主体，你就会对最初的信息变得*盲目*。因为布尔运算丢失了信息，所以当你再次需要它时，你必须回溯来恢复它。

丹为布尔盲提供了这个解决方案，“布尔测试让你*看*，选项让你*看*”

丹指的是 [ML](https://en.wikipedia.org/wiki/ML_%28programming_language%29) 中的`option`型。在 Elm，我们称之为`Maybe`型。Dan 的意思是，布尔只告诉你某个东西是否存在。`Maybe`型告诉你它是否存在，当它可用时给你。让我们用`Maybe String`重写我们的例子。

```
type alias Person =
    { time : Maybe String }

whatTimeIsIt : Person -> Maybe String
whatTimeIsIt person =
    person.time

currentTime : Person -> String
currentTime person =
    case whatTimeIsIt person of
        Just time ->
            time

        Nothing ->
            "Does anybody really know what time it is?" 
```

我们将`time`字段更新为`Maybe String`。然后，我们添加一个返回`person.time`的`whatTimeIsIt`函数。在`currentTime`里面我们现在调用`whatTimeIsIt`并对结果进行模式匹配。如果这个人有时间，那么我们可以立即在`Just`中访问它。不需要先用 if-else 表达式检查。如果这个人没有时间，即`Nothing`，那么我们返回默认值。

如果时间不存在，我们不会意外地访问它，因为编译器会强制执行`Maybe`类型约束。

不过，我们还有一个问题。`Just`中的时间可能是空字符串，这是一个无效的时间。接下来我们来解决这个问题。

## 使用时间。简便操作系统

我们需要一个更好的时间编码类型来避免空字符串。幸运的是，Elm 有一个处理时间的包，叫做 [elm/time](https://package.elm-lang.org/packages/elm/time/latest/) 。它提供了一个`Posix`类型来表示 Unix 时间，即从 UTC 时间 1970 年 1 月 1 日午夜起已经过去的时间。我们可以使用`Posix`类型，然后在需要时将其转换成格式化的时间。

```
import Time exposing (Posix, toHour, toMinute, utc)

type alias Person =
    { time : Maybe Posix }

whatTimeIsIt : Person -> Maybe Posix
whatTimeIsIt person =
    person.time

currentTime : Person -> String
currentTime person =
    case whatTimeIsIt person of
        Just time ->
            String.fromInt (toHour utc time)
                ++ ":"
                ++ String.fromInt (toMinute utc time)

        Nothing ->
            "Does anybody really know what time it is?" 
```

我们导入`Time`模块并公开`Posix`、`toHour`、`toMinute`和`utc`。我们将`time`字段更改为`Maybe Posix`，并更新`whatTimeIsIt`的类型注释。在`currentTime`的`Just`分支中，由于`Posix`类型，我们现在知道我们有一个有效的时间。我们使用`toHour`和`toMinute`函数以及`String.fromInt`和`utc`时区来构建格式化的字符串时间。

这太棒了。由于静态类型，编译器将强制我们的代码只访问存在的有效时间。

我们可以更进一步改进这段代码。如果一个人没有时间，那么就是`Nothing`。但是，这并不能解释*为什么*这个人没有时间。我们可以用自己的自定义类型替换`Maybe`。

```
type CurrentTime
    = CurrentTime Posix
    | NoWatch
    | InAHurry

type alias Person =
    { time : CurrentTime }

currentTime : Person -> String
currentTime person =
    case whatTimeIsIt person of
        CurrentTime time ->
            String.fromInt (toHour utc time)
                ++ ":"
                ++ String.fromInt (toMinute utc time)

        NoWatch ->
            "I don't have the time."

        InAHurry ->
            "Sorry, I'm in a hurry." 
```

我们引入一个带有三个构造函数的`CurrentTime`自定义类型，`CurrentTime`、`NoWatch`和`InAHurry`。`CurrentTime`构造器包装`Posix`。然后我们将`time`字段改为`CurrentTime`。在`currentTime`函数中，我们处理所有三个构造函数。`CurrentTime`分支与之前的`Just`分支保持不变。`NoWatch`和`InAHurry`分支各自返回一个字符串，描述这个人为什么没有时间。

现在，我们已经使代码更加精确地解释了为什么一个人没有时间，并且用自定义类型将更好的业务领域规则编码到代码中。另外，我们仍然有编译器来确保我们只能在`CurrentTime`分支中访问有效的时间。

## 你学到了什么

在本文中，您了解了布尔返回值会导致布尔盲。您看到了布尔盲会导致人为错误，让代码在不正确的位置访问数据。您发现内置的定制类型，如`Maybe`或您自己的定制类型允许您测试和访问数据的存在。此外，编译器确保您只在数据真正可用时才访问数据。尝试重构一些您自己的代码，用更有意义的自定义类型替换布尔返回值，使您的代码更易于维护。

要了解更多关于如何有效构建 Elm 应用程序的信息，请从[实用程序员](https://pragprog.com/book/jfelm/programming-elm)那里拿一本我的书[Elm 编程](https://programming-elm.com)。