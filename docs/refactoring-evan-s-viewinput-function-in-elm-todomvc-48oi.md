# 在 elm-todomvc 中重构 Evan 的 viewInput 函数

> 原文：<https://dev.to/dwayne/refactoring-evan-s-viewinput-function-in-elm-todomvc-48oi>

我不喜欢我们必须编写自己的 [onEnter](https://github.com/evancz/elm-todomvc/blob/07e3d4e5259f337d5eba781319b3a916e28aca99/src/Main.elm#L248) 函数来让 [viewInput](https://github.com/evancz/elm-todomvc/blob/07e3d4e5259f337d5eba781319b3a916e28aca99/src/Main.elm#L230) 工作。

通过将 input 元素包装在 form 元素中，我们可以实现相同的目标，编写更简单的代码并保持 HTML 的语义。

```
viewInput : String -> Html Msg
viewInput task =
  header
    [ class "header" ]
    [ h1 [] [ text "todos" ]
    , form
        [ onSubmit Add ]
        [ input
            [ class "new-todo"
            , placeholder "What needs to be done?"
            , autofocus True
            , value task
            , name "newTodo"
            , onInput UpdateField
            ]
            []
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您键入描述并按下`ENTER`时，表单上会触发一个[提交](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/submit_event)事件。 [onSubmit](https://package.elm-lang.org/packages/elm/html/latest/Html-Events#onSubmit) 事件处理程序使用 [preventDefault](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault) 检测提交事件，并将`Add`消息传播给`update`函数。

你怎么想呢?这个版本好点了吗？