# 让我们在 Elm 中构建一个随机报价机——第 3 部分

> 原文：<https://dev.to/dwayne/let-s-build-a-random-quote-machine-in-elm-part-3-3ek7>

[昨天](https://dev.to/dwayne/let-s-build-a-random-quote-machine-in-elm-part-2-49cl)我们将 HTML 移植到 Elm 并重构了代码。

今天，我们将让“新报价”按钮工作，在应用程序初始加载时从远程源获取报价，我们将通过设置一个标志来配置远程源的 URL。

让我们开始吧。

## 使“新报价”按钮工作

当点击“新报价”按钮时，将显示一个新报价，并且某些元素的颜色将会改变。

### 准备随机应变

为了在我们的应用中处理随机性，我们需要能够
[命令](https://guide.elm-lang.org/effects/) Elm 的运行时系统为我们生成随机值。

[Browser.element](https://package.elm-lang.org/packages/elm/browser/1.0.1/Browser#element) 允许我们使用[命令](https://guide.elm-lang.org/effects/)。

让我们编辑`src/Main.elm`来使用[浏览器。元素](https://package.elm-lang.org/packages/elm/browser/1.0.1/Browser#element)。

```
module Main exposing (main)

import Browser

main : Program () Model msg
main =
  Browser.element
    { init = init
    , view = view
    , update = update
    , subscriptions = always Sub.none
    }

-- MODEL

type alias Model =
  { quote : Quote
  }

init : () -> (Model, Cmd msg)
init _ =
  ( { quote = defaultQuote
    }
  , Cmd.none
  )

-- UPDATE

update : msg -> Model -> (Model, Cmd msg)
update _ model =
  ( model
  , Cmd.none
  )

-- VIEW

view : Model -> Html msg
view { quote } =
  div [ class "background" ]
    [ div []
        [ viewQuoteBox quote
        , -- ...
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

### 显示随机报价

安装[榆树/随机](https://package.elm-lang.org/packages/elm/random/1.0.0/)。

```
$ elm install elm/random 
```

Enter fullscreen mode Exit fullscreen mode

然后，对`src/Main.elm` :
进行如下编辑

```
import Random

main : Program () Model Msg

type alias Model =
   { quote : Quote
   , quotes : List Quote
   }

init : () -> (Model, Cmd msg)
init _ =
  ( { quote = defaultQuote
    , quotes = allQuotes
    }
  , Cmd.none
  )

allQuotes : List Quote
allQuotes =
  [ defaultQuote
  , { content = " Transferring your passion to your job is far easier than finding a job that happens to match your passion."
    , author = "Seth Godin"
    }
  , { content = "Less mental clutter means more mental resources available for deep thinking."
    , author = "Cal Newport"
    }
  , { content = "How much time he saves who does not look to see what his neighbor says or does or thinks."
    , author = "Marcus Aurelius"
    }
  , { content = "You do not rise to the level of your goals. You fall to the level of your systems."
    , author = "James Clear"
    }
  ]

type Msg
  = ClickedNewQuote
  | NewQuote Quote

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    ClickedNewQuote ->
      ( model
      , Random.generate NewQuote (Random.uniform defaultQuote model.quotes)
      )

    NewQuote newQuote ->
      ( { model | quote = newQuote }
      , Cmd.none
      )

view : Model -> Html Msg

viewQuoteBox : Quote -> Html Msg
viewQuoteBox quote =
  div [ class "quote-box" ]
    [ -- ...
    , div [ class "quote-box__actions" ]
        [ -- ...
        , -- ...
        , div []
            [ button
                [ -- ...
                , onClick ClickedNewQuote
                ]
                [ text "New quote" ]
            ]
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

当点击“New quote”按钮时,`ClickedNewQuote`消息是由 Elm 的运行时系统创建的
,它最终用该消息调用我们的`update`函数。

我们的`update`函数的`ClickedNewQuote`分支被选中，它向 Elm 的运行时发送一个命令，告诉运行时从我们的报价列表中选择一个随机报价，并将其包装在一个`NewQuote`消息中。

当运行时创建了`NewQuote`消息时，运行时最终会用该消息调用我们的`update`函数。选择了`NewQuote`分公司，我们的模型用新的报价更新。

### 随机改变颜色

以下是您需要进行的编辑:

```
import Html.Attributes exposing (..., style, ...)

type alias Model =
  { -- ...
  , color : Color
  }

type alias Color = String

init : () -> (Model, Cmd msg)
init _ =
  ( { -- ...
    , color = defaultColor
    }
  , Cmd.none
  )

defaultColor : Color
defaultColor =
  "#333"

allColors : List Color
allColors =
  [ "#16a085"
  , "#27ae60"
  , "#2c3e50"
  , "#f39c12"
  , "#e74c3c"
  , "#9b59b6"
  , "#fb6964"
  , "#342224"
  , "#472e32"
  , "#bdbb99"
  , "#77b1a9"
  , "#73a857"
  ]

type Msg
  = ClickedNewQuote
  | NewQuoteAndColor (Quote, Color)

update msg model =
  case msg of
    ClickedNewQuote ->
      ( model
      , Random.generate NewQuoteAndColor <|
          Random.pair
            (Random.uniform defaultQuote model.quotes)
            (Random.uniform defaultColor allColors)
      )

    NewQuoteAndColor (newQuote, newColor) ->
      ( { model | quote = newQuote, color = newColor }
      , Cmd.none
      )

view { quote, color } =
  div
    [ class "background"
    , style "background-color" color
    ]
    [ div []
        [ viewQuoteBox quote color
        , -- ...
        ]
    ]

viewQuoteBox : Quote -> Color -> Html Msg
viewQuoteBox quote color =
  div
    [ class "quote-box"
    , style "color" color
    ]
    [ -- ...
    , div [ class "quote-box__actions" ]
        [ div []
            [ viewIconButton "twitter" (twitterUrl quote) color ]
        , div []
            [ viewIconButton "tumblr" (tumblrUrl quote) color ]
        , div []
            [ button
                [ -- ...
                , style "background-color" color
                , onClick ClickedNewQuote
                ]
                [ text "New quote" ]
            ]
        ]
    ]

viewIconButton : String -> String -> Color -> Html msg
viewIconButton name url color =
  a [ -- ...
    , style "background-color" color
    ]
    [ i [ class ("fa fa-" ++ name) ] [] ] 
```

Enter fullscreen mode Exit fullscreen mode

现在，当点击“新报价”按钮时，报价和颜色都会被随机选中。

### 添加颜色过渡

最后，让我们添加一些颜色过渡，使颜色变化平稳。

向`assets/styles.css` :
添加两个新类

```
/* Transitions */

.has-color-transition {
  transition: color 2s;
}

.has-background-color-transition {
  transition: background-color 2s;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，将`has-color-transition`类添加到报价框:

```
viewQuoteBox quote color =
  div
    [ class "quote-box has-color-transition"
    , style "color" color
    ]
    [ -- ...
    ] 
```

Enter fullscreen mode Exit fullscreen mode

并且，将`has-background-color-transition`类添加到背景和按钮:

```
div
  [ class "background has-background-color-transition"
  , style "background-color" color
  ]
  [ -- ...
  ]

button
  [ -- ...
  , class "button has-background-color-transition"
  , style "background-color" color
  , -- ...
  ]
  [ text "New quote" ]

a [ -- ...
  , class "icon-button has-background-color-transition"
  , style "background-color" color
  ]
  [ i [ class ("fa fa-" ++ name) ] [] ] 
```

Enter fullscreen mode Exit fullscreen mode

更多详情，请点击[此处](https://github.com/dwayne/elm-random-quote-machine/blob/tutorial/tutorial/step-05.md)。

## 从遥远的地方得到报价

我们将使用的引文来自这里的[和](https://gist.githubusercontent.com/dwayne/ff832ab1d4a0bf81585870369f984ebc/raw/46d874a29e9efe38006ec9865ad67b054ef312a8/quotes.json)。

## 解码语录

报价以 JSON 格式返回，格式如下:

```
{  "quotes":  [  {  "content":  "Life isn't about getting and having, it's about giving and being.",  "author":  "Kevin Kruse"  },  {  "content":  "Whatever the mind of man can conceive and believe, it can achieve.",  "author":  "Napoleon Hill"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要安装 [elm/json](https://package.elm-lang.org/packages/elm/json/1.1.3/) 来解码响应。

```
$ elm install elm/json 
```

Enter fullscreen mode Exit fullscreen mode

让我们写解码器。

```
import Json.Decode as D

-- DECODERS

quotesDecoder : D.Decoder (List Quote)
quotesDecoder =
  D.field "quotes" (D.list quoteDecoder)

quoteDecoder : D.Decoder Quote
quoteDecoder =
  D.map2 Quote
    (D.field "content" D.string)
    (D.field "author" D.string) 
```

Enter fullscreen mode Exit fullscreen mode

`quoteDecoder`可以解码格式为
的 JSON

```
{  "content":  "...what matters in the long run is sticking with things and working daily to get better at them.",  "author":  "Angela Duckworth"  } 
```

Enter fullscreen mode Exit fullscreen mode

并且，`quotesDecoder`可以解码格式为
的 JSON

```
{  "quotes":  [  {  "content":  "...what matters in the long run is sticking with things and working daily to get better at them.",  "author":  "Angela Duckworth"  },  {  "content":  "Don't judge. Teach. It's a learning process.",  "author":  "Carol S. Dweck"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

### 获取报价

安装 [elm/http](https://package.elm-lang.org/packages/elm/http/2.0.0/) 。

```
$ elm install elm/http 
```

Enter fullscreen mode Exit fullscreen mode

并且，编写以下代码，以便在应用程序初始加载时获取报价:

```
import Http

init : () -> (Model, Cmd Msg)
init _ =
  ( -- ...
  , getQuotes "https://gist.githubusercontent.com/dwayne/ff832ab1d4a0bf81585870369f984ebc/raw/46d874a29e9efe38006ec9865ad67b054ef312a8/quotes.json"
  )

type Msg
  = -- ...
  | GotQuotes (Result Http.Error (List Quote))

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    -- ...

    GotQuotes (Ok remoteQuotes) ->
      ( { model | quotes = remoteQuotes }
      , Cmd.none
      )

    GotQuotes (Err _) ->
      ( model, Cmd.none )

-- COMMANDS

getQuotes : String -> Cmd Msg
getQuotes url =
  Http.get
    { url = url
    , expect = Http.expectJson GotQuotes quotesDecoder
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 通过标志传递远程源的 URL

为了使 URL 易于配置，我们将通过一个
[标志](https://guide.elm-lang.org/interop/flags.html)将它传递到应用程序中。

编辑`index.html`。

```
Elm.Main.init({
  node: document.getElementById("app"),
  flags: "https://gist.githubusercontent.com/dwayne/ff832ab1d4a0bf81585870369f984ebc/raw/46d874a29e9efe38006ec9865ad67b054ef312a8/quotes.json"
}); 
```

Enter fullscreen mode Exit fullscreen mode

编辑`src/Main.elm`。

```
main : Program String Model Msg
main =
  -- ...

init : String -> (Model, Cmd Msg)
init url =
  ( -- ...
  , getQuotes url
  ) 
```

Enter fullscreen mode Exit fullscreen mode

### 在应用程序初始加载时选择一个随机报价

```
init : String -> (Model, Cmd Msg)
init url =
  ( -- ...
  , Cmd.batch
      [ generateNewQuoteAndColor allQuotes
      , getQuotes url
      ]
  )

update msg model =
  case msg of
    ClickedNewQuote ->
      ( model
      , generateNewQuoteAndColor model.quotes
      )

    -- ...

generateNewQuoteAndColor : List Quote -> Cmd Msg
generateNewQuoteAndColor quotes =
  Random.generate NewQuoteAndColor <|
    Random.pair
      (Random.uniform defaultQuote quotes)
      (Random.uniform defaultColor allColors) 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你！你已经到达终点了。查看[演示](https://dwayne.github.io/elm-random-quote-machine/)以检查您是否能够跟进并重新创建应用程序。

更多详情，请点击[此处](https://github.com/dwayne/elm-random-quote-machine/blob/tutorial/tutorial/step-06.md)。

现在去使用你在本教程中学到的东西来构建你的下一个应用程序。如果你遇到困难，请随时向我寻求建议或帮助。

编码快乐！