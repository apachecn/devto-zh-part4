# 第 21-30 天

> 原文：<https://dev.to/theoriginalbpc/100-days-of-code-round-two-days-21-30-1fhk>

*最初发表于 2019 年 6 月 30 日 BritishPandaChick Codes。我对原来的帖子做了一些调整，这样它就可以为实际开发工作服务了。*

我刚刚完成了第 30 天，所以是时候再次回顾 100 天的代码了。今天的帖子是查看第 21-30 天的条目。现在我的作品集已经更新并在网上发布了，我已经开始了两个新项目。

一个是自由代码营前端库项目部分的 markdown 预览器项目。另一个是另一个组合网站 30 天，30 个网站的编码挑战。markdown 预览器和作品集并不是我唯一在做的新项目。

我完成了第 11-20 天的报价机项目。我还参与了几个可汗学院的编码项目。

如果你想了解更多关于免费代码营的课程，你可以去免费代码营网站注册一个免费账户。编码挑战是开始构建项目的好方法。30 天，30 个站点是一个编码挑战，开发人员用 30 天来构建项目。每天你都会收到一个提示，告诉你要建造什么，然后你就可以开始建造了。您可以使用下面的链接注册。

[== >点击这里报名 30 天，30 个站点！](https://www.subscribepage.com/30days30sites)

## 第 21 天:2019 年 6 月 21 日

前 30 分钟是添加风格。我设法改变了字体。我尝试了一些 Bootstrap 类，看看他们是否能在几分钟内同时看到引文和作者。

Bootstrap 不是我最喜欢使用的东西之一，因为我的项目总是根据屏幕大小而不同，所以我发现自己经常以几种大小查看页面，看看一切看起来如何。我添加了一个 h3 和 img 标签作为我的头的一部分。我用标题和主标签组织了 HTML。

我花了一些时间试图找出报价框的大小，但决定先玩玩高度，并在移动到报价框之前，让标题按照我想要的方式组织。我试着用一些引导文档将图像居中，但发现我在填充方面运气更好。

在最后的 30 分钟里，我在 tweet 按钮上添加了 Twitter 图标，并对这个项目做了一些最后的调整。这包括在页面底部添加页脚和添加颜色。我试着在手机版的新报价和推文按钮之间留出一些空间。然而，当我检查 iPhone 预览版时，这些变化并没有显示在开发工具上。

现在我打算让它保持现在的样子，但是也许有一天我会回到这个项目中去改变它。这个项目的风格非常简单。我想过在页面上的引号周围添加引号，但决定保持原样。

当我运行最终测试时，tweet 按钮测试失败了。当我再运行几次测试时，所有的测试都通过了。在摆弄页面时，结果是 tweet quote 按钮只是一直显示相同的引用，即使页面上的文本显示的是不同的引用。明天我会看看我是否能解决这个问题，并让按钮在每次点击新报价按钮时重置报价。

## 第 22 天:2019 年 6 月 22 日

我的重点是试图修复 quote machine 项目上的 tweet 按钮，这样 tweet 就会随着新的报价而改变。前 30 分钟只是尝试使用免费代码营论坛的提示来解决 tweet 引用问题。现在，当你点击 tweet 按钮时，窗口打开，显示第一次点击按钮时页面上的报价。如果引用发生变化，当点击按钮时，tweet 不会改变，仍然显示第一次点击按钮时的引用。

到目前为止，我还没有在自由代码营论坛上找到太多关于这个问题的建议。现在，我正在尝试使用一个函数，并基于我在论坛上看到的一个方法创建单独的变量。在过去的 30 分钟里，我使用了第二种方法。

这种方法需要使用一个小部件，但它不起作用。我在自由代码营论坛中发现了一个复杂的方法，它可以追溯到使用 encodeURIComponent 方法。起初，它不起作用。

我一直被带到另一个页面，说有一个错误。然而，这是由于我使用了以前尝试中使用的一些变量。一旦我改变了变量，一切都开始工作。现在 tweet 按钮又开始工作了。

我正在考虑在明天的推文中添加引号，看看效果如何。我最大的问题是测试。我的测试一开始在 new-quote 按钮和 tweet 按钮上失败了。然而，再次运行测试通过了一切。明天我会用一些时间来运行测试，并仔细检查一切。

## 第 23 天:2019 年 6 月 23 日

前 30 分钟是在报价机上做最后的测试。当测试在页面开始时正确运行时，只有 11/12 的测试通过。一种情况是 10/12 的测试都通过了。

似乎当按钮没有被按下时，测试失败了。然而，在测试了按钮并运行了自由代码 Camp 测试之后，所有 12 个测试都通过了。如果按钮没有被使用，测试似乎会失败，但是当它们被使用时会工作。

在运行了几次测试之后，我决定留下我的代码，因为它们都符合用户故事。我可能会在未来重新访问这个项目，看看我是否可以在没有任何问题的情况下第一次进行测试。

在那 30 分钟的会议中，剩下的时间是建立 markdown 预览器项目。我承认我对这个项目的内容感到困惑。所以我可能会拿自由代码营这个例子来玩一玩，看看他们想让我构建什么。

我认为这是一个使用 React 的好项目，所以我花了一些时间阅读 React 网站上的文档，以确保我设置正确。我设法在 markdown 预览器上添加了两个 HTML 标签，但我没有感觉到这个项目的灵感，因为我不知道我应该建立什么。

对于第二个 30 分钟的会议，我决定重做我已经做了 30 天的一些网站，30 个网站与自由代码营项目一起做。我对 markdown 预览器的项目概述感到不知所措和困惑，所以我认为 30 天，30 个站点项目将帮助我理清思路，并创建项目来展示我正在创建的编码挑战的示例。在这个环节的前几分钟，我们只是把一切都组织好，做好准备。

我在 HTML 代码上有了一个良好的开端，并添加了一些标签。我明天会试着完成 HTML。我的 30 天 30 个站点组合的目标是更好地了解 Bootstrap，并让它在移动设备上工作，因此我将更多地关注这个组合的 Bootstrap，而不是我刚刚更新的组合站点。

## 第 24 天:2019 年 6 月 24 日

在昨天的会议之后，我和其他人的 markdown 预览器一起玩，包括自由代码营的例子，以便更好地理解这个项目是如何工作的。这有助于我更好地了解我将构建什么。我在 markdown 预览器中添加了一些 HTML 标签。

有些标签只是在我构建的时候作为参考，所以它们不会一直出现在项目中。前 30 分钟，我们只是试图在 CodePen 上正确地设置 react。这比我预期的要花更多的时间，因为我在 CodePen 中为 React 添加了一些错误的链接，所以我仔细检查了文档以确保我做得正确。

我还没有开始大量的 JavaScript。我明天的目标是，在我开始尝试弄清楚用户故事之前，确保一切都是相互关联的。

在最后的 30 分钟里，我在 30 天，30 个站点组合网站上添加了更多的 HTML。我添加了谷歌字体和字体真棒。我确实开始了一些 CSS 样式，但是我的一些文本没有改变。

当我明天开始添加更多样式时，我将仔细检查我的样式表链接。我也想玩布局，并开始把这个网站的不同版本放在一起，看看我可以使用什么引导类。

## 第 25 天:2019 年 6 月 25 日

最初的 30 分钟只是试图再次检查，确保 React 设置正确。React 文档撒谎了。我花了超过一分钟的时间来设置。结果是我之前添加的 HTML 代码抛弃了 React 中的所有内容。

所以我改变了类，删除了几天前添加的大部分原始代码。一旦我将类改为 id 并删除了所有多余的代码，一切都慢慢开始工作了。我花了前 30 分钟试图让“你好，世界”出现，所以我主要是从设置中排除所有的错误。

我花时间阅读文档并使用文档示例代码来显示“Hello World”。“Hello World”一出现，我就开始着手完成第一个用户故事。我明天会在这方面多下功夫。

最后的 30 分钟是为 30 天，30 个站点的组合页面添加样式。我发现了为什么我的字体没有出现在我的网站上。原来我用的是字体大小而不是字体系列。

我今天没有花太多时间在 HTML 上，但是我删除了底部的联系人部分。我开始添加样式。我花了很多时间试图改变大屏幕的颜色。

明天我将尝试一下标题的容器类，看看是否可以为标题添加背景色。一旦我完成了大部分的样式，我想利用这段时间开始尝试引导类，看看在不同的移动设备上是什么样子。

## 第 26 天:2019 年 6 月 26 日

前 10 分钟是为可汗学院的课程建立一个项目。建立这个项目让我一直在考虑建立我自己的博客网站。Khan Academy 版本非常简单，因为该项目旨在让用户练习字体系列、字体风格和行高。做这个项目很简单，但是我一直在思考重新建立我自己的博客网站的过程。

博客网站完成后，我把前 30 分钟的剩余时间花在了 Markdown 预览器上。大部分时间是开始设置 React 组件。由于我在阅读 React 上的文档和查看 StackOverflow 上的帖子，所以我没有做很多编码工作。我意识到开始用户故事 1 的最好方式是创建一个表单。

所以在 render 方法中，我开始为编辑器和预览区域设置表单。我在 ReactJS 网站上找到了一些关于 textarea 标签的文档。我明天将尝试文档的例子，看看我如何定制它成为这个项目的编辑器。

最后的 30 分钟是对 30 天，30 个站点组合进行更多的 CSS 更新。我为高光和页脚部分添加了一些 CSS。今天我回到 HTML，开始摆弄引导类。

现在我正在做我的作品集网站的移动版。到目前为止，一切都不像我以前的大多数 Bootstrap 项目那样到处都是。我需要做的就是完成页脚，然后我将继续设计该产品组合的响应式平板电脑版本。我只需要将 float 属性添加到页脚即可。

## 第 27 天:2019 年 6 月 27 日

我在 Markdown 预览器中添加了 React 组件，然后删除了它。textarea 文档对我不起作用。完成文档后，我没有得到任何结果。

我一直在看 React 文档，但我也看了几个 YouTube 视频，只是为了仔细检查我的设置。我认为我最初的 HTML 抛弃了我的代码，所以我删除了它。事实证明，我写 JSX 是错误的，尤其是在渲染方法上。

在今天的会议中，我一直在 CodePen 中收到一条错误消息。最初我在 render 元素中使用 h1 和 h2 标记，错误消息说相邻的标记必须用封闭标记包裹。经过几次尝试后，当我将 h1 和 h2 标记改为 div 标记时，错误消息终于消失了。我决定只在 HTML 中保留 header 标签，并专注于在 render 方法中构建 react。

在最后的 30 分钟里，我在 30 天，30 个站点组合站点的页脚玩了一圈。我还没能完成我对页脚的设想。我需要在我的一些元素上使用 row 类来将它们放在一行中。

我明天会试试那个。我今天没有做手机版的作品集。当我在编写代码教程时，我看到柯尔特·斯蒂尔用 Bootstrap 在一个应用程序中制作了一个导航条，我想在这个项目中尝试一下。

所以我的大部分时间都花在了查看引导文档和在页面顶部获得一个导航栏上。我移除了超大屏幕类和一些标题样式，但是我在导航条上有了一个新的开始。我只需要弄清楚如何让导航链接并排排列，就像文档中那样，而不是一个接一个。一旦 navbar 完成，我将继续让网站在不同的屏幕上运行。

## 第 28 天:2019 年 6 月 28 日

前 30 分钟是让编辑器显示文本，让预览器显示结果。我确实使用了一些文档和本·布鲁克的 YouTube 教程来帮助。我最大的问题是编辑在我工作的时候消失了。在我工作的时候，我的编辑器和预览在我处理 JS 文件的时候一直试图消失。

我将容器元素放在一个 div 标签中，所有内容都留在页面上。我在为预览编辑器设置 dangerouslySetInnerHTML 属性时忘记了一个下划线。这给了我更多的 CodePen 错误。

我还需要记住，在使用 React 时，我可以使用自结束标记。在阅读文档和观看 Ben 的视频时，我注意到一些 div 和 textarea 标签可以用作自结束标签。现在我在 React 中的标签有开始和结束标签，但是我必须记住在将来的 React 项目中尝试一些自结束标签。

在最后的 30 分钟里，我完成了导航条的工作。我重新安排了一些 nav 类，并用 row 类添加了一个 div。这使得导航条上的所有东西都排成一行。

但是，标题和导航项似乎很接近。我试着使用 justify-content-end 来看看当我向右浮动列表项时，这是否会右对齐列表项。没用。

这些东西只是呆在同一个地方。我决定让它们保持现在的样子，这样我就可以专注于投资组合的其他领域。我用 row 类添加了更多的 div 标签到高亮和页脚。

最初，亮点项目并不在一行中。这是因为我为这些列使用了类。一旦我将列从 col-xs 更改为 col，元素就会整齐地排列成一行。明天我将调整列的大小，这样页面上的所有内容看起来就不会那么拥挤了。

## 第 29 天:2019 年 6 月 29 日

我花了 30 分钟试图解决 markdown 预览器的第五个用户故事。现在测试失败了。我一直在使用本·布鲁克的教程，并在谷歌上搜索文档，看看我做错了什么。我运气不好。

现在，列表降价是错误消息中要解决的问题。明天我将通读列表文档，看看我做错了什么。我仍然会检查其余的降价，以确保它们通过，但测试指向列表项目，所以我会看看我能在谷歌上找到什么。但是我喜欢学习降价文档。将来有机会的时候，我一定会尝试一些降价活动。

最后 30 分钟是更多的工作在 30 天，30 个网站的投资组合页面。我通过改变列的大小来修改页脚。我大部分时间都在玩作品集网站的精彩部分。

我添加了一个标题最新的文章和一个 h4 标签来标注每篇文章的日期。我删除了之前添加的视频，用图片替换了 p 标签。我确实给这些元素添加了一些样式，但是大多数样式变化都是添加了填充和高度属性。

我在标题顶部的导航栏上添加了一些样式，比如改变颜色，添加背景色。最重要的是，我移除了一个容器类，navbar 和 headline 被包裹起来，这样它就像页脚一样扩展了整个容器。明天，在我开始观察网站在不同屏幕尺寸下的外观之前，我将修复高亮部分的图像填充，并为页脚做一些最终的样式更改。

## 第 30 天:2019 年 6 月 30 日

在前 30 分钟，我设法完成了 markdown 预览器的所有功能。我做了更多的谷歌搜索，并使用本布鲁克的 YouTube 教程来帮助我。原来是我忘了正文和连字符之间有个空格。我一输入空格，预览器就显示出列表项目的要点，并且测试通过了。

剩下的时间都花在了让可选测试通过上。我觉得 break 测试有点傻，因为我花了很多时间在谷歌上搜索如何使用 JavaScript 将回车符转换成换行符，而一直以来都有标记为. js 的文档说明如何以更简单的方式来做这件事。我对渲染器对象的理解是正确的。

我最初尝试使用 inside marked.setOptions。然而，Ben Brooke 的方法要简单得多，它包括创建一个渲染器变量，然后创建一个设置渲染器的链接和目标属性的函数。所有的强制测试都通过了，可选测试也通过了，所以我只需要关注页面的样式。

最后 30 分钟正在对 30 天 30 个站点的组合站点进行更改。我花了几分钟时间来玩填充高光图像和页脚。大部分时间都花在了看网站如何在不同的屏幕尺寸下工作。这意味着玩 Bootstrap 类，看看它们在开发工具中的样子。

我设法完成了手机版，因为没有太多的变化。我最大的问题是试图为页脚和高亮帖子找到正确的引导类。明天我将看看我是否需要为平板电脑屏幕尺寸做出任何改变，并确保桌面版本需要任何改变。

## 结论

明天开始第 31 天。我仍然会在 markdown previewer 项目上工作，复查测试并添加样式。30 天，30 个网站的投资组合只需要多一点点的风格。那么它也会被完成。

你是在做 100 天代码挑战还是 30 天 30 个站点？在下面的评论区分享你的进步。