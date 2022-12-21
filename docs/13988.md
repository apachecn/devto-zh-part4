# freeCodeCamp 在 Elm 中构建的 JavaScript 计算器项目

> 原文：<https://dev.to/dwayne/freecodecamp-s-javascript-calculator-project-built-in-elm-6c6>

我喜欢思考如何完成这个项目。为了让它更有挑战性，我有两个额外的目标。

1.  在 UI 和应用程序逻辑之间有一个清晰的[分离。](https://dev.to/dwayne/separating-application-logic-from-the-ui-in-elm-8nj)
2.  举个例子,测试(不管类型如何)有助于捕捉 bug。

在这个项目的自述文件中，你可以在这里读到更多我的想法。

这里是我的[演示](https://dwayne.github.io/elm-calculator/)和[源代码](https://github.com/dwayne/elm-calculator)。

无论您选择只是阅读代码还是构建项目，您都将学到以下内容:

1.  如何通过一个具体的例子来分离 UI 和应用程序逻辑。
2.  如何用联合类型来表示一个状态机。状态机是驱动计算器输入的东西，它已经过[全面测试](https://github.com/dwayne/elm-calculator/blob/8524029c188d519e3a34c5e45d6b535ac129c625/tests/Test/Calculator.elm)。
3.  如何在 Elm 中编写[测试？](https://github.com/dwayne/elm-calculator/tree/8524029c188d519e3a34c5e45d6b535ac129c625/tests/Test)
4.  即使在类型化的函数式语言中测试的重要性。
5.  如何实现一个[栈](https://github.com/dwayne/elm-calculator/blob/8524029c188d519e3a34c5e45d6b535ac129c625/src/Stack.elm)。
6.  如何实现[调车场算法](https://github.com/dwayne/elm-calculator/blob/8524029c188d519e3a34c5e45d6b535ac129c625/src/Expr.elm#L17)。
7.  一个[简洁的算法](https://github.com/dwayne/elm-calculator/blob/8524029c188d519e3a34c5e45d6b535ac129c625/src/Rational.elm#L101)，用于将有理数显示为小数，例如将`1/6`显示为`0.1(6)`。
8.  如何迭代地将特性添加到应用程序中，[逐个提交](https://github.com/dwayne/elm-calculator/commits/master)。

如果你想边做边学，甚至为项目做贡献，那么你可以试着解决这些问题。

如果你有兴趣通过教程或其他方式对这个项目进行更详细的分析，请在评论中告诉我。