# 我每天使用的四个 Firefox 开发工具

> 原文：<https://dev.to/gabe/four-firefox-dev-tools-i-use-daily-212l>

我爱火狐，这不是秘密。我发现它是一个敏捷的浏览器，内置了一些非凡的开发工具。

我周围的大多数人都使用 Chrome，(或者最近的 [Brave](https://brave.com/) )并且对他们的开发工具深信不疑。我承认它们非常棒，我甚至在几个星期后切换到 Brave，发现它是一个非常棒的浏览器。然而，在家里和工作场所连续使用数周后，我发现自己又回到了 Firefox。当我启动开发工具并开始工作时，感觉就像回到了家。就功能而言，FF 和 Chrome 几乎相同，*几乎*。我将简要强调一些我认为是火狐开发工具中最有用的特性。

#### 高亮显示

[![](img/84b1a6c5f6fd4bf4845ec05a6c0f0666.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OUWc8NWv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d7l7lzltwij632c7u9rx.gif) 
对我来说，Firefox 的 inspector 工具最有用的功能之一是能够高亮显示页面上与我选择的任何选择器匹配的元素。在我上面的 GIF 示例中，我突出显示了主页上的每个 anchor 标签，其中包含类`.wp-block-latest-posts`。当我试图找出哪个或哪些项目属于某个类名，或者需要某个`div` box-model 的概览，同时我检查它正上方或正下方的元素时，这个特性非常方便。即使您在开发工具中切换到不同的选项卡，元素也会保持高亮显示。因此，当您切换到“字体”选项卡或浏览器控制台时，可以保持某个元素高亮显示。这是一个有多种用途的小功能。

#### Fonts

[![](img/c886d2564e031a57389959c1cf2dd472.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FEudyfl0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z6y50ennjcpgzi24wg9g.gif) 
【字体】标签是 Firefox 开发工具中较新的一项。在这里，您可以很好地了解当前页面上使用的字体，以及快速定制其外观的控件——从`font-weight`到`font-size`，甚至是`line-height`。

#### 变化

我经常使用检查工具来突出显示项目，并随时随地修改 CSS。如果我喜欢我所看到的，我会回溯我的修改，并在我的文本编辑器中写出代码。“更改”选项卡方便地将我在长时间的会话中在 inspector 工具中所做的所有 CSS 修改集中在一起，以便我可以快速了解我到底做了什么，Firefox 甚至给了我直接从这里复制和粘贴的能力。痛苦地回溯无数元素的日子已经一去不复返了，因为我不再记得我到底做了什么来让一些东西看起来像预期的那样。
[![](img/ae1f26c0a2f31d58a19313207faf4be1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--8p-TSvZ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0q59wdiqevqs10jboddm.png)

#### 草稿栏

[![](img/83e53e9746d214d625cd16d0c77cec0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eRh8VNOK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5em4872zfnp5ep54g2tv.gif) 
便签簿是凉的；它允许你写出一些 JavaScript 并在准备好的时候执行它。它还允许您检查 JS 对象。我喜欢 Scratchpad 的一点是，它更像一个文本编辑器，而不是一个浏览器控制台。我可以像在 VS 代码中一样写出我的 JS，它不会执行，直到我告诉它运行，这与控制台相反，有时我会在编写一些 JavaScript 的过程中运行，这要归功于在 return 键上的滑动手指。

还有很多其他很酷的 Firefox 开发工具特性我在这篇文章中没有提到，比如[实时 CSS 提示](https://twitter.com/nicolaschevobbe/status/1135798960361263104)，或者 [CSS 网格检查器](https://twitter.com/jensimmons/status/1126570959715098624)。要进一步了解火狐的开发工具能做什么，你可以跳到他们的[文档](https://developer.mozilla.org/en-US/docs/Tools)，我也建议关注他们的 [twitter](https://twitter.com/FirefoxDevTools) 以了解他们开发工具的最新进展。享受朋友！