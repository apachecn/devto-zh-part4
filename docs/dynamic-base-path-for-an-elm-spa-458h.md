# Elm SPA 的动态基本路径

> 原文：<https://dev.to/bigardone/dynamic-base-path-for-an-elm-spa-458h>

在构建 Elm SPA 仪表板时，我遇到了以下问题。在本地开发环境中，访问它的 URL 是`http://localhost:1234`，这是包的默认 URL，Elm SPA 在`/`中挂载，因此 Elm navigation 按预期处理任何内部路由，如`/projects`或`/tasks`。将它部署到生产环境中时出现了问题，因为基本 URL 与根路径不匹配。换句话说，它看起来有点像[https://nifty-minsky-538aab.netlify.com/private/admin/](https://nifty-minsky-538aab.netlify.com/private/admin/)，其中`/private/admin/`是应用程序的基本路径，并且该路径可以根据环境而改变，这使得 Elm 导航变得棘手，尤其是在解析 URL 以获取当前路径时。我想避免使用 URL 片段，所以我是这样解决的。

### <基础> HTML 元素

首先，我需要一种方法将动态基本 URL 添加到任何内部 Elm 路由的前面。经过一些研究，我找到了方便的 [< base >](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) HTML 元素，它指定了一个文档中包含的所有相对 URL 的基本 URL。这意味着如果你设置了`<base href="http://localhost:1234/private/admin/">`，我添加的任何相关链接，比如`<a href="projects">Projects</a>`，都会自动指向`http://localhost:1234/private/admin/projects`，这正是我要找的。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <base href="{{ BASE_URL }}">
  </head>
  <body>
    <main></main>
    <script src="./js/index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

根据您使用的技术栈，使用环境变量为当前环境设置`href`值很容易。

### 将基本路径传递给 Elm 应用程序

现在我有了一种方法来设置应用程序所有内部链接的基本 URL，我需要一种方法让 Elm 知道这个基本路径，这种方法非常简单，使用标志和 [baseURI](https://developer.mozilla.org/en-US/docs/Web/API/Node/baseURI) 属性:

```
import { Elm } from '../src/Main.elm';

const basePath = new URL(document.baseURI).pathname;

Elm.Main.init({
  node: document.querySelector('main'),
  flags: { basePath },
}); 
```

Enter fullscreen mode Exit fullscreen mode

`baseURI`基本上返回文档的位置，除非您设置了`<base>`，在这种情况下，它总是返回设置的值。我只需要路径，因此从`URL(document.baseURI).pathname`获取路径，并将其作为标志传递给`Elm.Main.init`函数。

### 榆树路由和基路径

我总是喜欢尽快定义应用程序路线，这有助于我理解如何构建它。此外，在这个特殊的例子中，路由是问题的根源，也是解决方案本身，所以让我们看看我实现的`Route`模块:

```
-- src/Route.elm

module Route exposing
    ( Route(..)
    , fromUrl
    , toString
    )

import Url exposing (Url)
import Url.Parser as Parser exposing (Parser)

type Route
    = Home
    | Projects
    | Tasks
    | NotFound

parser : Parser (Route -> b) b
parser =
    Parser.oneOf
        [ Parser.map Home Parser.top
        , Parser.map Projects (Parser.s "projects")
        , Parser.map Tasks (Parser.s "tasks")
        ]

-- ... 
```

Enter fullscreen mode Exit fullscreen mode

这几乎是在 Elm 中定义路由及其解析器的标准方式，我不需要做任何特别的修改就能让它工作。然而，`fromUrl`和`toString`两个功能都需要与平常稍有不同:

```
-- src/Route.elm

-- ...

fromUrl : String -> Url -> Route
fromUrl basePath url =
    { url | path = String.replace basePath "" url.path }
        |> Parser.parse parser
        |> Maybe.withDefault NotFound

toString : Route -> String
toString route =
    case route of
        Home ->
            ""

        Projects ->
            "projects"

        Tasks ->
            "tasks"

        NotFound ->
            "not-found" 
```

Enter fullscreen mode Exit fullscreen mode

`fromUrl`接受一个`basePath`和一个`Url`参数并返回一个`Route`。第一个参数是在 Elm 应用程序初始化时传递给它的标志，为了获得相应的`Route`，我们只需要从它的路径中删除`basePath`，并像往常一样解析它。请记住，这只适用于使用文档头中设置的`<base>`元素构建的 URL。最后但同样重要的是，`toString`函数提供了一种为给定的`Route`构建相对路径的便捷方式。

### 把一切粘合在一起

解决了 URL 的解析之后，构建应用程序的其余部分就相当简单了。我们来看看一些实现细节:

```
-- src/Main.elm

module Main exposing (main)

import Browser exposing (Document)
import Browser.Navigation as Navigation
import Html as Html exposing (Html)
import Route exposing (Route)
import Url exposing (Url)

-- MODEL

type alias Flags =
    { basePath : String }

type alias Model =
    { flags : Flags
    , navigation : Navigation
    }

type alias Navigation =
    { key : Navigation.Key
    , route : Route
    }

init : Flags -> Url -> Navigation.Key -> ( Model, Cmd Msg )
init ({ basePath } as flags) url key =
    ( { flags = flags
      , navigation =
            { key = key
            , route = Route.fromUrl basePath url
            }
      }
    , Cmd.none
    )

-- ...

-- MAIN

main : Program Flags Model Msg
main =
    Browser.application
        { init = init
        , update = update
        , view = view
        , subscriptions = subscriptions
        , onUrlRequest = UrlRequested
        , onUrlChange = UrlChange
        } 
```

Enter fullscreen mode Exit fullscreen mode

我通常使用名为`Flags`的自定义类型将传递给应用程序的标志存储在模型中，在这个特定的示例中，它只包含`basePath`。我还喜欢存储一个`Navigation`自定义元素，其中包含导航所需的`Navigation.Key`和当前路线。`init`函数使用先前定义的`Route.fromUrl`函数从浏览器的 URL 和`basePath`标志设置当前路线。但是，它也需要在每次 URL 改变时进行设置:

```
-- src/Main.elm

-- ...

-- UPDATE

type Msg
    = UrlRequested Browser.UrlRequest
    | UrlChange Url

update : Msg -> Model -> ( Model, Cmd Msg )
update msg ({ flags, navigation } as model) =
    case msg of
        UrlRequested urlRequest ->
-- ...

        UrlChange url ->
            ( { model
                | navigation =
                    { navigation
                        | route = Route.fromUrl flags.basePath url
                    }
              }
            , Cmd.none
            ) 
```

Enter fullscreen mode Exit fullscreen mode

这就是我如何使用`Route.toString`函数:
创建导航链接

```
Html.div
    []
    [ Html.a
        [ Html.href <| Route.toString Route.Home ]
        [ Html.text "Home" ]
    , Html.a
        [ Html.href <| Route.toString Route.Projects ]
        [ Html.text "Projects" ]
    , Html.a
        [ Html.href <| Route.toString Route.Tasks ]
        [ Html.text "Tasks" ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已；一切都很顺利。老实说，在得到这个解决方案之前，我尝试了不同的方法，包括定制的`Url`解析器，这对我来说很难理解。您是否面临过同样的问题？如果有，希望这个解决方案下次对你有帮助，如果你有不同的解决方法，请分享:)

编码快乐！

[现场演示](https://nifty-minsky-538aab.netlify.com/private/admin/)
[源代码](https://github.com/bigardone/elm-dynamic-base-path)