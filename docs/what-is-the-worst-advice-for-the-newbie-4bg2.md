# 对新手最糟糕的建议是什么？

> 原文：<https://dev.to/stereobooster/what-is-the-worst-advice-for-the-newbie-4bg2>

今天我在新人聚会上。(又来了。经过上一次，我给新手写了[建议。)而这次聚会激发了我写这篇文章的灵感。](https://stereobooster.com/posts/advice-for-the-newbie/)

## 使用文本编辑

我和一个人谈过，他得到了以下建议:学习 CSS 和 HTML 使用 [TextEdit](https://support.apple.com/en-za/guide/textedit/welcome/mac) (或者另一个纯文本编辑器)。据说这样你可以更快地学习语法。顾问认为使用现代代码编辑器(像 code 或者 sublime)会让学习变慢。

多么糟糕的建议。没有突出显示，没有可以突出显示匹配括号的编辑器，很难编写代码，当你转到下一行时，括号会自动缩进光标。对一个新手来说这是浪费时间。甚至更现代的编辑器通常内置了语法检查器，所以不用写东西，保存文件，重新加载浏览器，你会在编辑器中看到错误。

为什么你要花时间在机器能做而且能做得更快的事情上？机器可以检查语法，自动修改格式。

你**需要懂语法才能写程序**，但是没有理由拒绝机器的帮助。

## 学习 Python(学习 JS)

有人问学习 JavaScript 的最好方法是什么。建议是学习 Python。等等，什么？所以推理如下，JS 很混乱(我同意)，Python 没那么混乱，所以如果你学了 Python 的基础，你可以转到 JS。

意图是好的，但是建议是糟糕的。一个新手会花时间学习 Python，然后会转到 JS，会发现比学习 Python 之前还要混乱，因为雄辩的 Python 和雄辩的 JS 有点不一样。在 JS 中，有一个事件循环、async/await 和可怕的强制规则，您应该避免这些规则。

更好的建议是:学习 JS，但要学习它最好的部分，例如避免三重相等的强制，使用`let` / `const`避免作用域问题(`var`)，使用箭头函数避免`this`问题，等等。

也许有 ESLint config 有好的实践，比如 [eslint-config-react-app](https://www.npmjs.com/package/eslint-config-react-app) ，但是没有 React specific。

不要误会我的意思 **Python 作为一种语言是可以的**，如果你想学习 Python 请这样做，但建议学习 Python 以了解 JS...

## 学习网页包

这个人问如何开始现代网络开发。建议是:坐下来，从头开始编写 web pack 配置，这样你就会明白谁是 bundler 的工作人员，然后你就可以编写网站了。

Webpack 很好，但是它是一个非常复杂的工具。它向最终用户暴露了太多意外的复杂性。错误消息令人困惑。版本迁移很难。文件有时会丢失。这对新手来说太难学了。我认为这个人在完成某件事之前就会放弃。

你为什么要把这作为第一步？可以用 Parcel，不需要配置——没什么好学的(几乎没什么)。或者你可以使用 create-react-app，它隐藏了 webpack。

**Webpack 作为软件还可以**，但是对于新手来说很难，不需要，因为你可以从其他工具开始，以后再学 Webpack。

## 对新手最糟糕的建议是什么？

分享你的坏建议的例子(在 dev.to 或 twitter 上)。

> 阿什利·尤里乌斯在 Unsplash 上拍摄的照片