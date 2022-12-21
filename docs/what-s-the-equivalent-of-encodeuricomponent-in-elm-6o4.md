# Elm 中的 encodeURIComponent 相当于什么？

> 原文：<https://dev.to/dwayne/what-s-the-equivalent-of-encodeuricomponent-in-elm-6o4>

我正在 Elm 中构建一个[随机报价机](https://learn.freecodecamp.org/front-end-libraries/front-end-libraries-projects/build-a-random-quote-machine/)应用程序，需要创建一个 [tweet web intent URL](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/guides/web-intent.html) 。

> tweet web intent 使得从链接撰写和发布 tweet 变得容易。

假设您想为以下报价创建一个 tweet web intent URL:

> 更少的精神混乱意味着更多的精神资源可用于深度思考。~ Cal Newport，[深度工作:在纷乱的世界中专注成功的法则](http://www.calnewport.com/books/deep-work/)

## JavaScript 中的

您可以执行以下操作:

```
content = 'Less mental clutter means more mental resources available for deep thinking.'
author = 'Cal Newport'

text = encodeURIComponent('"' + content + '" ~ ' + author)
url = 'https://twitter.com/intent/tweet?hashtags=quotes&text=' + text 
```

Enter fullscreen mode Exit fullscreen mode

要查看应该会发生什么，点击[这里](https://twitter.com/intent/tweet?hashtags=quotes&text=%22Less%20mental%20clutter%20means%20more%20mental%20resources%20available%20for%20deep%20thinking.%22%20~%20Cal%20Newport)。

我使用 [encodeURIComponent](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent) ，因为`text`参数需要一个 URL 编码的字符串。

## [榆树中的](#in-elm-019)(0.19)

这花了我一段时间，但我终于通过使用[榆树搜索](https://klaftertief.github.io/elm-search/?q=String%20-%3E%20String)找到了我需要的东西。

**N.B.** *要进入 Elm Search，您可以进入 [Elm Packages](https://package.elm-lang.org/) ，点击“资源”下的“花式搜索”链接。我就是这么找到的。*

该函数位于 [elm/url](https://package.elm-lang.org/packages/elm/url/1.0.0) 包中，名为 [percentEncode](https://package.elm-lang.org/packages/elm/url/1.0.0/Url#percentEncode) 。它给出了与 JavaScript 的`encodeURIComponent`函数相同的行为。

然而，它的文档建议您使用 [Url。改为建造者](https://package.elm-lang.org/packages/elm/url/1.0.0/Url-Builder)。

阅读完文档后，您会得到如下的结果:

```
import Url.Builder exposing (crossOrigin, string)

content = "Less mental clutter means more mental resources available for deep thinking."
author = "Cal Newport"

url =
  crossOrigin "https://twitter.com"
    [ "intent", "tweet" ]
    [ string "hashtags" "quotes"
    , string "text" ("\"" ++ content ++ "\" ~ " ++ author)
    ] 
```

Enter fullscreen mode Exit fullscreen mode

这正是我想要的。

## 总结

在 Elm 中，`encodeURIComponent`的等价物被称为`percentEncode`，它可以在`elm/url`包中找到。但是，它应该只用于非常定制的情况。有[网址。构建器](https://package.elm-lang.org/packages/elm/url/1.0.0/Url-Builder)，它抽象出了 URL 编码位以及其他东西，这就是你想要用来创建你的 URL 的东西。