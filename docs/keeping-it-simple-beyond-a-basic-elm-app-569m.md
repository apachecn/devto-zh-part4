# 保持简单，超越基本的 Elm 应用程序

> 原文：<https://dev.to/elmupdate/keeping-it-simple-beyond-a-basic-elm-app-569m>

当你想超越一个基本的 Elm 代码库时，人们通常会把你指向 Richard Feldman 的 [elm-spa-example](https://github.com/rtfeldman/elm-spa-example) 。

除非你有信心你正在走向一个多页，功能丰富的盛会，那么这是矫枉过正。

是的，它证明了在 Elm 中你实际上可以把事情分解成子组件，委托模型、消息和订阅，这并不意味着你应该这样做。

另一方面，也有人说，你可以把所有的东西都放在一个文件里，用一个有几百行的巨大的更新功能。嗯。说真的，你可能不想等待滚动或者跳到特定的无意义的行号。

最近，我有一个中等规模的 web 应用程序要开始构建，所以我试图走中间路线。从一些基本原则开始。

1.  保持简单-未来的维护依赖于它。
2.  尽量减少无意义的抽象和模式——不做应用程序想做的事情的代码会浪费每个人的时间。
3.  不要打榆树架构-更新功能改变模型是改变和调用命令。模型变化反映在视图中。查看事件、命令结果和订阅导致更多更新。这是一个良性的、相对整洁的单向循环。
4.  避免嵌套。尽可能保持单一真实来源模型的扁平化。请记住，所有事件都流经主更新，即使是委托的。
5.  不要让组件难以交互。一个 web 应用程序会在一个角落生成事件，这些事件会导致影响应用程序其他部分的模型更改和命令。子视图需要访问顶层和其他组件的数据。
6.  让查找相关代码变得容易。

我用一个文件解决方案从 [create-elm-app](https://github.com/halfzebra/create-elm-app) 开始，然后当我需要访问 Url 时升级到 Browser.application。

当我觉得更新功能变得有点滚动，我有一个用户编辑部分，这是新兴的，我把它移到自己的文件。正是在这一点上，我必须决定与代表团走多远。

有趣的是，我以前发现，很容易陷入这样一种情况，你需要来回调用你的主文件来获取类型和消息定义——导致循环引用。

我在过去发现，通过将所有类型放在一个名为 types 的文件中，将所有 msg 放在一个名为 msgs 的文件中，将公共函数放在一个名为 Helpers 的文件中，或者相应地放在一个名为 CommonViews 的文件中，可以解决这个问题。

所以我继续进行重构。这与不要在授权上发疯是一致的。我们试图避免的是让你想得太多的东西，就像 elm-spa 中的例子:

```
updateWith : (subModel -> Model) -> (subMsg -> Msg) -> Model -> ( subModel, Cmd subMsg ) -> ( Model, Cmd Msg )
updateWith toModel toMsg model ( subModel, subCmd ) =
    ( toModel subModel
    , Cmd.map toMsg subCmd
    ) 
```

没人应该看这个！

简而言之，我推迟了更新功能，但没有推迟其他功能。

所以在主要更新中:

```
case msg of
    ----COMPONENT MESSAGES----
    ToEnv envMsg ->
        Env.update envMsg model

    ToUsers userMsg ->
        Users.update userMsg model
        ...

    ----TOP LEVEL MESSAGES----
        ... 
```

组件的包装消息通常意味着我正在进行组件组合(<

在类型文件中，通过使用注释分隔符，主模型定义被分成组件部分。

这有助于保持嵌套的上限，并保持真实的单一来源在头脑中。

它还意味着一个等价的 init 函数，尽管引用了空的特定于组件的记录，现在我将这些记录保存在它们各自的组件文件中。

```
type alias Model =
    { window : Window
    , messageToUser : Maybe String
    , env : Env
    , initialUrl : Url
    , navKey : Key
    ...

    -- User
    , users : List User
    , userSearchQuery : String
    ... 
```

对于 Msgs 文件，包含组件包装的主文件有一个主消息类型，每个组件有单独的消息类型。

```
type Msg
    = ResizeDevice Int Int
    | ToEnv EnvMsg
    | ToUsers UserMsg
    ...

type EnvMsg
    = GetEnv
    | EnvResult (Result Http.Error ConfigEnv)
    ...

type UserMsg
    = ClearSearchUsers
    | SearchUsers
    ... 
```

我可以把组件消息和组件的其他部分放在一起。然而，尽可能在一个视图中保存所有可能的消息是有用的。

这样做的净效果是 main 有一个非常清晰的显示应用程序结构的更新功能，没有无意义的抽象。

组件文件具有它们自己的更新功能，该功能处理它们自己的消息类型，并通过任何组件消息的顶层返回到单一真实源模型。

```
update : UserMsg -> Model -> ( Model, Cmd Msg )
update userMsg model =
    case userMsg of
        SearchUsers ->
        ... 
```

组件中需要一点嵌套访问，但是只需要一个级别，所以是可管理的。

组件文件还包含这些组件的相关视图。

我还没有在我的应用程序中找到授权订阅的理由，可能是因为我还没有很多订阅。我也没有做太多的路由。

除此之外，总的来说，我对中小型应用的结果非常满意。有一个脚手架最小化。能够找到、添加和重构代码变得非常容易。