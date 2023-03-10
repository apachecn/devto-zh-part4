# 在榆树上玩“with”

> 原文：<https://dev.to/elmupdate/playing-with-with-in-elm-3p9m>

受 Charlie Koster 的 Elm 中的 [With*函数的启发，我决定在最近的工作项目中引入一组‘With’函数。](https://medium.com/@ckoster22/with-functions-in-elm-a88dc0e1f851)

Koster 的例子允许您创建一个默认按钮，然后连接到一个或多个修饰符，而不是每次都定义修饰符的完整记录。

从他的代码:

```
default "Small Call to Action" Msg
    |> makeSolid
    |> makeDisabled
    |> view 
```

我的代码处于更糟糕的状态。对于我的调用函数，我有未命名的参数，而不是作为单个参数的选项记录。随着应用程序的增长，这些参数也在增长，到了某个程度，参数的用途并不总是显而易见。

```
singleCheckBox 
    editingUser.blocked 
    (ToUsers << ChangeBlock) 
    "login blocked" 
    False 
```

为了简单起见，我决定将所有的参数都放在选项参数中，以受益于它们都被命名，而不是将所需的参数分离出来并作为常规参数保留。

然后，当我开始创建“with”语句时，我很快意识到“with”修饰语的数量将会激增，然而很多需要的是输入类型之间的相同概念。

所以我决定使用允许我们通用的记录修改语法。现在，我可以使用一个“with”修饰符来表示标题，例如，跨我拥有的不同按钮和输入。

```
withTitle : String -> { a | title : String } -> { a | title : String }
withTitle value record =
    { record | title = value } 
```

你可能会意识到，我没有科斯特那样多的类型安全。他的只是一个按钮的模块，并与它绑定在一起。我有一个收集不同输入的模块。我还不清楚如果我进一步锁定它，是否还能保留通用方法。

我还阅读了由托马斯·卡根发起的交流，其中讨论了管道与属性列表的利弊。

我认为属性列表方法更符合我代码的其余部分。我还将选项类型定义和默认值放在了每个选项的上下文中。

```
type alias InputOptions =
    { textContent : String
    , changeMsg : String -> Msg
    , placeholderText : String
    , title : String
    , titleNote : String
    , disable : Bool
    }

inputBox : List (InputOptions -> InputOptions) -> Element Msg
inputBox optionModifiers =
    let
        defaults =
            { textContent = ""
            , changeMsg = \_ -> Noop
            , placeholderText = ""
            , title = ""
            , titleNote = ""
            , disable = False
            }

        options =
            List.foldl
                identity
                defaults
                optionModifiers
    in
    ... 
```

我将这种方法应用于我拥有的所有输入视图——既有输入视图，也有呈现输入集合的视图。我列出了 20 个我可以通用的“with”修饰语。

现在我对输入的调用看起来很棒

```
standardButton
    [ withButtonMsg (ToUsers (ConfirmDeleteUser userId))
    , withTitle "Delete Permanently"
    , withColor color.red
    ] 
```

我可以指出，除了增加了 foldl 语句之外，代码可读性更好。最重要的是，现在只有超过默认值的参数被传递，它们都被命名。