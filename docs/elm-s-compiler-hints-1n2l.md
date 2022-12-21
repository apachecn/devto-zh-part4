# Elm 的编译器提示

> 原文：<https://dev.to/dwayne/elm-s-compiler-hints-1n2l>

当你运行`elm init`时，它会给你一个链接，你可以根据自己的意愿去阅读关于`elm init`的所有内容。😍

以前，它是一个网页。一个看似孤独的网页，没有链接到任何东西。我通常会想，“我在哪里可以得到更多这样的信息？”🤔

偶尔，我会在 Elm 做一些异端的事情，比如可变阴影，我会被指向另一个孤独的网页，从 Evan 神父那里得到一个冗长的谈话。😬我又一次想知道“我怎样才能做更多那样的坏事并得到更多这样的信息？”😈

然后昨天发生了。

我正在阅读通过 Lambda 演算进行函数式编程的介绍，我决定尝试用 Elm 和 boom:
编写自应用函数

```
---- Elm 0.19.0 ----------------------------------------------------------------
Read <https://elm-lang.org/0.19.0/repl> to learn more: exit, help, imports, etc.
--------------------------------------------------------------------------------
> selfApply = \s -> s s
-- INFINITE TYPE ----------------------------------------------------------- elm

I am inferring a weird self-referential type for s:

3| selfApply = \s -> s s
                ^
Here is my best effort at writing down the type. You will see ∞ for parts of the
type that repeat something already printed out infinitely.

    ∞ -> a

Staring at this type is usually not so helpful, so I recommend reading the hints
at <https://elm-lang.org/0.19.0/infinite-type> to get unstuck! 
```

Enter fullscreen mode Exit fullscreen mode

当我导航到链接[https://elm-lang.org/0.19.0/infinite-type](https://elm-lang.org/0.19.0/infinite-type)时，这次它重定向到一个降价文件。读完之后，我向上翻了一个目录。

我发现了隐藏的珠宝。😭

[![Yes](img/9484fb043aac8f8273ef9f0381a10aca.png)](https://i.giphy.com/media/10cuF5z6VYzqW4/giphy.gif)

自己看，[提示](https://github.com/elm/compiler/tree/9d97114702bf6846cab622a2203f60c2d4ebedf2/hints)。

**免责声明:** *我知道艾凡的意思是，当你真正遇到问题时，在你准备好消化信息的时候，你会在需要的时候发现这些提示。所以请记住，你不需要一次读完这些东西就能在 Elm 中高效工作。*

在这篇文章中提到:

*   [https://elm-lang.org/0.19.0/init](https://elm-lang.org/0.19.0/init)
*   [https://elm-lang.org/0.19.0/shadowing](https://elm-lang.org/0.19.0/shadowing)
*   [https://elm-lang.org/0.19.0/infinite-type](https://elm-lang.org/0.19.0/infinite-type)