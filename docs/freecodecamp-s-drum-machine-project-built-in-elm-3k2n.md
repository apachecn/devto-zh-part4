# freeCodeCamp 的架子鼓项目建在榆树

> 原文：<https://dev.to/dwayne/freecodecamp-s-drum-machine-project-built-in-elm-3k2n>

这个[鼓机](https://learn.freecodecamp.org/front-end-libraries/front-end-libraries-projects/build-a-drum-machine/)项目是一个令人满意的完成。

有时我怀疑我是否能让一切按我想要的方式运行。但是最后一切都解决了，项目进展得非常顺利。

这里是我的[演示](https://dwayne.github.io/elm-drum-machine/)和[源代码](https://github.com/dwayne/elm-drum-machine)。

无论您选择只是阅读代码还是构建项目，您都将学到以下内容:

1.  如何[播放音频](https://github.com/dwayne/elm-drum-machine/blob/1c17d9f41af5a4f56b5817f0e05f520f3579c3da/src/Main.elm#L138)。
2.  如何为上下键事件写一个[自定义解码器](https://github.com/dwayne/elm-drum-machine/blob/1c17d9f41af5a4f56b5817f0e05f520f3579c3da/src/Main.elm#L179)。
3.  如何使用 [Process.sleep](https://package.elm-lang.org/packages/elm/core/1.0.2/Process#sleep) 做相当于 JavaScript 的`setTimeout`。
4.  如何让 Elm 输出像[不换行空格](https://github.com/dwayne/elm-drum-machine/blob/1c17d9f41af5a4f56b5817f0e05f520f3579c3da/src/Main.elm#L283)这样的特殊 HTML 字符？

我向你挑战，在榆树市建造什么东西，任何东西。这是学习语言的最好方法。