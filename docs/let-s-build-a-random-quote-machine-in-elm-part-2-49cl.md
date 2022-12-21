# 让我们在 Elm 中构建一个随机报价机——第 2 部分

> 原文：<https://dev.to/dwayne/let-s-build-a-random-quote-machine-in-elm-part-2-49cl>

昨天我们完成了结构(HTML)和样式(CSS)。

今天，我们将把 HTML 移植到 Elm，并以这样一种方式组织代码，以便将来添加功能时更加方便。

## 将 HTML 移植到 Elm

转到项目的根目录并运行`elm init`。

```
$ cd path/to/random-quote-machine
$ elm init 
```

Enter fullscreen mode Exit fullscreen mode

在提示符下按 ENTER 键，为您创建一个`elm.json`文件和一个空的`src`目录。

`elm.json`文件跟踪应用程序的依赖关系和其他元数据。

目录是你放置组成你的应用程序的所有 Elm 文件的地方。在这种情况下，您需要一个文件，`src/Main.elm`。

**n . b .**T3】你可以阅读 https://elm-lang.org/0.19.0/init 的[了解更多`elm init`。](https://elm-lang.org/0.19.0/init)

现在开始创建该文件。

```
$ touch src/Main.elm 
```

Enter fullscreen mode Exit fullscreen mode

然后，将其编辑为包含以下内容:

```
module Main exposing (main)

import Html exposing (Html, a, blockquote, button, cite, div, footer, i, p, span, text)
import Html.Attributes exposing (autofocus, class, href, target, type_)

main : Html msg
main =
  div [ class "background" ]
    [ div []
        [ div [ class "quote-box" ]
            [ blockquote [ class "quote-box__blockquote"]
                [ p [ class "quote-box__quote-wrapper" ]
                    [ span [ class "quote-left" ]
                        [ i [ class "fa fa-quote-left" ] [] ]
                    , text "I am not a product of my circumstances. I am a product of my decisions."
                    ]
                , footer [ class "quote-box__author-wrapper" ]
                    [ text "\u{2014} "
                    , cite [ class "author" ] [ text "Stephen Covey" ]
                    ]
                ]
            , div [ class "quote-box__actions" ]
                [ div []
                    [ a [ href "https://twitter.com/intent/tweet?hashtags=quotes&text=%22I%20am%20not%20a%20product%20of%20my%20circumstances.%20I%20am%20a%20product%20of%20my%20decisions.%22%20%E2%80%94%20Stephen%20Covey"
                        , target "_blank"
                        , class "icon-button"
                        ]
                        [ i [ class "fa fa-twitter" ] [] ]
                    ]
                , div []
                    [ a [ href "https://www.tumblr.com/widgets/share/tool?posttype=quote&tags=quotes&content=I%20am%20not%20a%20product%20of%20my%20circumstances.%20I%20am%20a%20product%20of%20my%20decisions.&caption=Stephen%20Covey&canonicalUrl=https%3A%2F%2Fwww.tumblr.com%2Fdocs%2Fen%2Fshare_button"
                        , target "_blank"
                        , class "icon-button"
                        ]
                        [ i [ class "fa fa-tumblr" ] [] ]
                    ]
                , div []
                    [ button
                        [ type_ "button"
                        , autofocus True
                        , class "button"
                        ]
                        [ text "New quote" ]
                    ]
                ]
            ]
        , footer [ class "attribution" ]
            [ text "by "
            , a [ href "https://github.com/dwayne/"
                , target "_blank"
                , class "attribution__link"
                ]
                [ text "dwayne" ]
            ]
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

一个 Elm 文件称为一个模块。第一行将模块命名为`Main`，并使`main`函数可供外界使用。

导入行从 [Html](https://package.elm-lang.org/packages/elm/html/1.0.0/Html) 和 [Html 导入各种函数。在您的`Main`模块中使用的属性](https://package.elm-lang.org/packages/elm/html/1.0.0/Html-Attributes)模块。

`Html`和`Html.Attributes`模块存在于 [elm/html](https://package.elm-lang.org/packages/elm/html/1.0.0/) 包中。如果您查看您的`elm.json`文件，您会看到`elm init`已经为您设置了`elm/html`包作为直接依赖项。这意味着你不需要安装它。

`main`函数包含了我们在`index.html`中发现的大部分内容，除了我们有函数调用而不是 HTML 标签和属性。

一般来说，

```
<foo attr1="a" attr2="b">bar</foo> 
```

Enter fullscreen mode Exit fullscreen mode

被翻译成:

```
foo [ attr1 "a", attr2 "b" ] [ text "bar" ] 
```

Enter fullscreen mode Exit fullscreen mode

其中`foo`和`text`在`Html`，而`attr1`和`attr2`在
和`Html.Attributes`。

唯一微小的区别是我们使用了 Unicode 代码点`\u{2014}`而不是`&mdash;` HTML 实体。

### 编译成 JavaScript

```
elm make src/Main.elm --output=assets/app.js 
```

Enter fullscreen mode Exit fullscreen mode

`Main`模块被编译成 JavaScript 并保存在`assets/app.js`中。

### 加载

编辑`index.html`并将`body`替换为以下内容:

```
<body>
  <div id="app"></div>
  <script src="assets/app.js"></script>
  <script>
    Elm.Main.init({
      node: document.getElementById("app")
    });
  </script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中查看`index.html`并观察到绝对没有任何变化。这是件好事。这意味着您忠实地将 HTML 转换为 Elm。

更多详情，请点击[此处](https://github.com/dwayne/elm-random-quote-machine/blob/tutorial/tutorial/step-03.md)。

## 重构 Elm 代码

我们将提取一些有用的函数，并添加一个新的类型。

### 提取`viewQuote`函数

从`main`函数中，获取包含`blockquote`的“HTML ”,并将其封装在一个名为`viewQuote`的函数中，该函数有两个参数。

```
viewQuote : String -> String -> Html msg
viewQuote content author =
  blockquote [ class "quote-box__blockquote"]
    [ p [ class "quote-box__quote-wrapper" ]
        [ span [ class "quote-left" ]
            [ i [ class "fa fa-quote-left" ] [] ]
        , text content
        ]
    , footer [ class "quote-box__author-wrapper" ]
        [ text "\u{2014} "
        , cite [ class "author" ] [ text author ]
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

然后，从`main`调用`viewQuote`。

```
viewQuote
  "I am not a product of my circumstances. I am a product of my decisions."
  "Stephen Covey" 
```

Enter fullscreen mode Exit fullscreen mode

### 提取`viewIconButton`函数

请注意

```
a [ href "https://twitter.com/intent/tweet?hashtags=quotes&text=%22I%20am%20not%20a%20product%20of%20my%20circumstances.%20I%20am%20a%20product%20of%20my%20decisions.%22%20%E2%80%94%20Stephen%20Covey"
  , target "_blank"
  , class "icon-button"
  ]
  [ i [ class "fa fa-twitter" ] [] ] 
```

Enter fullscreen mode Exit fullscreen mode

还有这个

```
a [ href "https://www.tumblr.com/widgets/share/tool?posttype=quote&tags=quotes&content=I%20am%20not%20a%20product%20of%20my%20circumstances.%20I%20am%20a%20product%20of%20my%20decisions.&caption=Stephen%20Covey&canonicalUrl=https%3A%2F%2Fwww.tumblr.com%2Fdocs%2Fen%2Fshare_button"
  , target "_blank"
  , class "icon-button"
  ]
  [ i [ class "fa fa-tumblr" ] [] ] 
```

Enter fullscreen mode Exit fullscreen mode

非常相似。

编写一个名为`viewIconButton`的函数来概括您看到的模式。

```
viewIconButton : String -> String -> Html msg
viewIconButton name url =
  a [ href url
    , target "_blank"
    , class "icon-button"
    ]
    [ i [ class ("fa fa-" ++ name) ] [] ] 
```

Enter fullscreen mode Exit fullscreen mode

返回到`main`，用相关的函数调用替换链接。

用于 Twitter:

```
viewIconButton "twitter" "https://twitter.com/intent/tweet?hashtags=quotes&text=%22I%20am%20not%20a%20product%20of%20my%20circumstances.%20I%20am%20a%20product%20of%20my%20decisions.%22%20%E2%80%94%20Stephen%20Covey" 
```

Enter fullscreen mode Exit fullscreen mode

并且，对于 Tumblr 的使用:

```
viewIconButton "tumblr" "https://www.tumblr.com/widgets/share/tool?posttype=quote&tags=quotes&content=I%20am%20not%20a%20product%20of%20my%20circumstances.%20I%20am%20a%20product%20of%20my%20decisions.&caption=Stephen%20Covey&canonicalUrl=https%3A%2F%2Fwww.tumblr.com%2Fdocs%2Fen%2Fshare_button" 
```

Enter fullscreen mode Exit fullscreen mode

我希望你看到 Twitter 和 Tumblr 的 URL 会根据引用的内容和作者而改变。因此，您需要一种方法来生成给定信息的 URL。

### 提取函数生成 Twitter 和 Tumblr 的 URL

安装 [elm/url](https://package.elm-lang.org/packages/elm/url/1.0.0/) 。它提供了构建 URL 所需的函数。

```
$ elm install elm/url 
```

Enter fullscreen mode Exit fullscreen mode

来自[网址。构建器](https://package.elm-lang.org/packages/elm/url/1.0.0/Url-Builder)模块导入 [crossOrigin](https://package.elm-lang.org/packages/elm/url/1.0.0/Url-Builder#crossOrigin) 和[字符串](https://package.elm-lang.org/packages/elm/url/1.0.0/Url-Builder#string)。

**n . b .***[字符串](https://package.elm-lang.org/packages/elm/url/1.0.0/Url-Builder#string)函数确保查询参数的值是[百分比编码的](https://tools.ietf.org/html/rfc3986#section-2.1)。*

```
import Url.Builder exposing (crossOrigin, string) 
```

Enter fullscreen mode Exit fullscreen mode

要生成 Twitter URL，编写`twitterUrl`函数:

```
twitterUrl : String -> String -> String
twitterUrl content author =
  let
    tweet = "\"" ++ content ++ "\"  \u{2014} " ++ author
  in
    crossOrigin "https://twitter.com"
      [ "intent", "tweet" ]
      [ string "hashtags" "quotes"
      , string "text" tweet
      ] 
```

Enter fullscreen mode Exit fullscreen mode

并且，为了生成 Tumblr URL，编写`tumblrUrl`函数:

```
tumblrUrl : String -> String -> String
tumblrUrl content author =
  crossOrigin "https://www.tumblr.com"
    [ "widgets", "share", "tool" ]
    [ string "posttype" "quote"
    , string "tags" "quotes"
    , string "content" content
    , string "caption" author
    , string "canonicalUrl" "https://www.tumblr.com/docs/en/share_button"
    ] 
```

Enter fullscreen mode Exit fullscreen mode

然后，更新`viewIconButton`函数调用的参数。

用于 Twitter:

```
viewIconButton "twitter" (twitterUrl "I am not a product of my circumstances. I am a product of my decisions." "Stephen Covey") 
```

Enter fullscreen mode Exit fullscreen mode

对于 Tumblr 使用:

```
viewIconButton "tumblr" (tumblrUrl "I am not a product of my circumstances. I am a product of my decisions." "Stephen Covey") 
```

Enter fullscreen mode Exit fullscreen mode

### 提取`viewQuoteBox`函数

```
viewQuoteBox : String -> String -> Html msg
viewQuoteBox content author =
  div [ class "quote-box" ]
    [ viewQuote content author
    , div [ class "quote-box__actions" ]
        [ div []
            [ viewIconButton "twitter" (twitterUrl content author) ]
        , div []
            [ viewIconButton "tumblr" (tumblrUrl content author) ]
        , -- ...
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

在`main`中使用。

```
main : Html msg
main =
  div [ class "background" ]
    [ div []
        [ viewQuoteBox
            "I am not a product of my circumstances. I am a product of my decisions."
            "Stephen Covey"
        , -- ...
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

## 添加`Quote`记录

```
type alias Quote =
  { content : String
  , author : String
  }

defaultQuote : Quote
defaultQuote =
  { content = "I am not a product of my circumstances. I am a product of my decisions."
  , author = "Stephen Covey"
  } 
```

Enter fullscreen mode Exit fullscreen mode

您需要更新`main`、`viewQuoteBox`、`viewQuote`、`twitterUrl`和`tumblrUrl`，以便使用`Quote`记录。

```
main =
  div [ class "background" ]
    [ div []
        [ viewQuoteBox defaultQuote
        , -- ...
        ]
    ]

viewQuoteBox : Quote -> Html msg
viewQuoteBox quote =
  div [ class "quote-box" ]
    [ viewQuote quote
    , div [ class "quote-box__actions" ]
        [ div []
            [ viewIconButton "twitter" (twitterUrl quote) ]
        , div []
            [ viewIconButton "tumblr" (tumblrUrl quote) ]
        , div []
            [ button
                [ type_ "button"
                , autofocus True
                , class "button"
                ]
                [ text "New quote" ]
            ]
        ]
    ]

viewQuote : Quote -> Html msg
viewQuote { content, author } =
  -- ...

twitterUrl : Quote -> Html msg
twitterUrl { content, author } =
  -- ...

tumblrUrl : Quote -> Html msg
tumblrUrl { content, author } =
  -- ... 
```

Enter fullscreen mode Exit fullscreen mode

下面是所有重构完成后的最终版本代码:

```
module Main exposing (main)

import Html exposing (Html, a, blockquote, button, cite, div, footer, i, p, span, text)
import Html.Attributes exposing (autofocus, class, href, target, type_)
import Url.Builder exposing (crossOrigin, string)

type alias Quote =
  { content : String
  , author : String
  }

defaultQuote : Quote
defaultQuote =
  { content = "I am not a product of my circumstances. I am a product of my decisions."
  , author = "Stephen Covey"
  }

main : Html msg
main =
  div [ class "background" ]
    [ div []
        [ viewQuoteBox defaultQuote
        , footer [ class "attribution" ]
            [ text "by "
            , a [ href "https://github.com/dwayne/"
                , target "_blank"
                , class "attribution__link"
                ]
                [ text "dwayne" ]
            ]
        ]
    ]

viewQuoteBox : Quote -> Html msg
viewQuoteBox quote =
  div [ class "quote-box" ]
    [ viewQuote quote
    , div [ class "quote-box__actions" ]
        [ div []
            [ viewIconButton "twitter" (twitterUrl quote) ]
        , div []
            [ viewIconButton "tumblr" (tumblrUrl quote) ]
        , div []
            [ button
                [ type_ "button"
                , autofocus True
                , class "button"
                ]
                [ text "New quote" ]
            ]
        ]
    ]

viewQuote : Quote -> Html msg
viewQuote { content, author } =
  blockquote [ class "quote-box__blockquote"]
    [ p [ class "quote-box__quote-wrapper" ]
        [ span [ class "quote-left" ]
            [ i [ class "fa fa-quote-left" ] [] ]
        , text content
        ]
    , footer [ class "quote-box__author-wrapper" ]
        [ text "\u{2014} "
        , cite [ class "author" ] [ text author ]
        ]
    ]

twitterUrl : Quote -> String
twitterUrl { content, author } =
  let
    tweet = "\"" ++ content ++ "\"  \u{2014} " ++ author
  in
    crossOrigin "https://twitter.com"
      [ "intent", "tweet" ]
      [ string "hashtags" "quotes"
      , string "text" tweet
      ]

tumblrUrl : Quote -> String
tumblrUrl { content, author } =
  crossOrigin "https://www.tumblr.com"
    [ "widgets", "share", "tool" ]
    [ string "posttype" "quote"
    , string "tags" "quotes"
    , string "content" content
    , string "caption" author
    , string "canonicalUrl" "https://www.tumblr.com/docs/en/share_button"
    ]

viewIconButton : String -> String -> Html msg
viewIconButton name url =
  a [ href url
    , target "_blank"
    , class "icon-button"
    ]
    [ i [ class ("fa fa-" ++ name) ] [] ] 
```

Enter fullscreen mode Exit fullscreen mode

更多详情，请点击[此处](https://github.com/dwayne/elm-random-quote-machine/blob/tutorial/tutorial/step-04.md)。

明天我们将使“新报价”按钮工作，这样当它被点击时，一个新的随机报价将被显示，某些元素的颜色将发生变化。然后，我们将安排在应用程序最初加载时从远程来源获取报价。最后，我们将通过一个标志来传递 URL，以确保它是易于配置的。