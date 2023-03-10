# 用 HTML 和 CSS Flexbox: Pt 编写一个响应式 Navbar。一

> 原文：<https://dev.to/ceeoreo/code-a-responsive-navbar-with-html-and-css-flexbox-pt-1-21p1>

*这篇文章最初刊登在[debugacademy.com](https://debugacademy.com/)上。*

导航栏是网站的主要组成部分之一。这就是帮助人们浏览你的网站的重要之处(因此得名)。也就是说，对于初学者来说，它们可能相当吓人。

不久前，我们创建了一个 navbar，作为我的 Debug Academy 课程的一部分。老实说，那对我来说并不太难。然后我们必须让它有反应。而且…我真的很讨厌它。我理解为什么响应式设计如此重要，但实际上让一个网站响应式设计可能是一个真正的痛苦。

但是，正如我之前所说的，所有网站都要有响应，这一点很重要。你会有用户从台式电脑和移动电话访问你的网站。移动用户的数量也在稳步增长。所以，作为一个网站开发者，你的工作就是确保你的网站看起来不错，不管你的观众使用什么设备。

所以让我们从网页的最顶端开始，创建一个响应式导航条。现在，我们将只对导航条进行编码。在我的下一篇文章中，我将带你完成使你的导航条响应的过程。

## 开始之前

在我们开始编码之前，有一些事情你需要知道。你需要对 HTML 有一个相当好的理解。如果你完全是初学者，没必要害怕。我最近写了一个全面的 HTML 指南。所以你可以在和我一起创建你的导航条之前检查一下。

[![ceeoreo](img/610094803b1a53300a27f16eb707978d.png)](/ceeoreo) [## HTML 101:你需要知道的入门知识

### CEO ra Ford 7 月 26 日 196 分钟阅读

#beginners #webdev #html #tutorial](/ceeoreo/html-101-what-you-need-to-know-to-get-started-5bmo)
Here are some other things you’ll need:

文本编辑器或 [Codepen](https://codepen.io/)
渴望学习
一些 CSS 经验(并非绝对必要)

## 第 0 步:`<head>`

[https://codepen.io/ceeoreo/embed/zgPmOr?height=600&default-tab=html&embed-version=2](https://codepen.io/ceeoreo/embed/zgPmOr?height=600&default-tab=html&embed-version=2)
这些你应该很熟悉，尤其是如果你已经读过我的 HTML 101 帖子的话。每个 HTML 文档都是这样开始的。标签对你来说可能是新事物。因为它被放在我们的`<head>`中，我们的用户不能在他们的屏幕上看到这个``。但是他们的浏览器会在页面标签上显示出来。

现在是时候为导航栏添加 HTML 了。

## 第一步:导航栏 HTML

这一段最吸引人的是`<nav>`。这是存储导航链接的 HTML 元素。然后我们在我们的`<nav>`中使用一个无序列表创建一个列表，也称为`<ul>`。使用`<a href>`标签，我们将链接放在一个`<li>`中。

正如你在下面看到的，我只是用“Tab”作为我的菜单项的占位符。但是你可以把任何你喜欢的东西放在这里。发疯吧。我还给我们的页面标题(“导航条教程”)设置了一个等同于“title”的 id。这是为了以后在我们的 CSS 中使用。
[https://codepen.io/ceeoreo/embed/wVPZZx?height=600&default-tab=html,result&embed-version=2](https://codepen.io/ceeoreo/embed/wVPZZx?height=600&default-tab=html,result&embed-version=2)

在这一点上，它离我们的最终目标还很远。这就是 CSS 的用武之地。

## 第二步:`<header>` CSS

首先，让我们开始给我们的导航栏背景颜色。我们使用`background-color`属性来做这件事。您可以使用[十六进制颜色代码](https://www.color-hex.com)来帮助选择您喜欢的任何颜色来定义该属性。如果你不喜欢十六进制颜色，你可以只输入一个基本颜色的名称，如红色或蓝色。

现在我们要定义导航条的尺寸。我们希望它在浏览器中横向延伸。我们将通过设置我们的宽度为 100%来完成。我还把高度设置为 60px。如你所见，仍有一些需要改进的地方，所以让我们继续努力。

## 第三步:`<ul>` CSS

我们不希望我们的菜单项像那样一个叠一个地放在一起。我们有很多方法可以解决这个问题。但我发现最简单的方法是将我们的显示器改为 flex。如果没有定义该属性，`display`默认为 block。这就是为什么列表项像积木一样堆叠在一起的原因。相反，使用`display: flex`将使它们按照我们想要的方式水平对齐。使用关键字 flex 实现了一种特殊的 CSS 样式，称为 Flexbox。我现在不会详细讨论这个问题。但是如果你想了解更多关于 Flexbox 的知识，你可以看看这篇来自 CSS-Tricks 的[文章。](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

我们还想去掉那些要点。我们使用设置为 none 的`list-style-type`属性来做到这一点。我们可以增加菜单项的字体大小，以便用户更容易阅读。`font-size`允许我们这样做。我把我的`font-size`设置为 20px。如果您愿意，可以使用不同的值。请记住，该值需要以像素为单位，即 px。

我希望每个`<li>`都被推到导航条的右侧。我可以通过使用名为`justify-content`的 Flexbox 属性来做到这一点。我们要给它取`flex-end`的值。这将把所有的`<li>`元素移动到导航栏的右边(或末端)。
[https://codepen.io/ceeoreo/embed/oKoRWG?height=600&default-tab=css&embed-version=2](https://codepen.io/ceeoreo/embed/oKoRWG?height=600&default-tab=css&embed-version=2)

## 第四步:`<li>` CSS

我们所有的元素都被揉成一团。他们需要一些空间。为此，我们使用了`margin`属性。设置为 20px，每个`<li>`元素的左、右、上、下各有 20 个像素的空间。同样，如果你想让它们离得更近或更远，你可以把像素数改成你想要的任何数字。我也决定把字体改成 Arial。我们可以用`font-family`属性做到这一点。
[https://codepen.io/ceeoreo/embed/voWwJj?height=600&default-tab=css&embed-version=2](https://codepen.io/ceeoreo/embed/voWwJj?height=600&default-tab=css&embed-version=2)

我们离最终目标越来越近了。只需再多一点 CSS，我们就能实现！

## 第五步:`#title` CSS

我喜欢将页面标题设置在导航栏的左侧。我们可以通过改变标题的右边距来实现。这就是我们在 HTML 中制作的标题 id 的用处。有了这个 id，我们可以将 CSS 应用到我们的页面标题上。使用 hashtag，我们可以在 CSS 中调用标题 id。我们现在将`margin-right`属性设置为 auto，以将标题移动到导航栏的左侧。
[https://codepen.io/ceeoreo/embed/BXmemM?height=600&default-tab=css&embed-version=2](https://codepen.io/ceeoreo/embed/BXmemM?height=600&default-tab=css&embed-version=2)

## 第六步:`<a href>` CSS

如你所见，我们的链接都是蓝色的。我不知道你怎么想，但我不喜欢这样。因此，我将使用下面的代码，将我们的 li 中的定位标记作为目标。我将设置颜色为黑色，并使用`text-decoration`属性，我将删除无下划线。

我们希望用户知道我们的列表项是链接。实现这一点的方法是，当用户将鼠标悬停在列表项上时，添加一个属性。这是使用`:hover`选择器完成的。我们将再次使用`text-decoration`属性，这次将其设置为下划线。每当用户将鼠标悬停在列表项上时，这段代码都会给列表项加下划线。

你可以自己尝试一下。
[https://codepen.io/ceeoreo/embed/xvPNmP?height=600&default-tab=result&embed-version=2](https://codepen.io/ceeoreo/embed/xvPNmP?height=600&default-tab=result&embed-version=2)

## 第七步:用 CSS 移除默认边距

我们就要完成我们的导航栏了！一件真正困扰我的事情是我们导航条周围的空白。作为一个初学者，我从来不知道为什么我编码的内容周围总是有一些空白。原来，默认情况下，浏览器会给我们的`<body>`增加一点空白。许多其他 HTML 元素也有默认边距。

这就是为什么我们的导航条周围有这么大的空间。谢天谢地，去掉这个空格非常容易。我们必须将我们身体的`margin`设置为 0 像素。我们的 ul 也有一个默认的保证金。同样，我们通过将 ul 的`margin`设置为 0px 来解决这个问题。【T2[https://codepen.io/ceeoreo/embed/wVPbNV?height=600&default-tab=css&embed-version=2](https://codepen.io/ceeoreo/embed/wVPbNV?height=600&default-tab=css&embed-version=2)

* * *

## 结论

这是我们的最终产品。
[https://codepen.io/ceeoreo/embed/wVPbNV?height=600&default-tab=result&embed-version=2](https://codepen.io/ceeoreo/embed/wVPbNV?height=600&default-tab=result&embed-version=2)
看起来真的很棒。但是，我们还有一些工作要做。我们的导航栏还没有响应。在我的下一篇文章中，我们将一步一步地让 navbar 具有响应性。

一如既往，请随时留下任何额外的评论或反馈。我一直很感激。