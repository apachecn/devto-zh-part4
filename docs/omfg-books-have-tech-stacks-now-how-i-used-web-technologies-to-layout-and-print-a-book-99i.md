# Omfg 图书现在有技术书库:我如何使用网络技术来排版和印刷一本书

> 原文：<https://dev.to/manuelbieh/omfg-books-have-tech-stacks-now-how-i-used-web-technologies-to-layout-and-print-a-book-99i>

不好意思，我不得不在这里引用 Kitze 的话:

> ![Kitze profile image](img/2a7c68f8967f91cc03bc37e09f5a1e60.png)Kitze[@ the Kitze](https://dev.to/thekitze)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ManuelBieh](https://twitter.com/ManuelBieh)[@ _ philpl](https://twitter.com/_philpl)Omfg 书籍现在有科技书库😂“因为 npm 关闭而缺少几页”2019 年 7 月 18 日上午 05:53[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1151731672582434821)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1151731672582434821)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1151731672582434821)3

几个月前，2019 年 4 月，我出版了一本关于 React 的[书。这是一本技术书。我这么说的意思是，它在很多方面都是一本技术书。当然，内容都是关于 React，React 如何工作，如何用 React 构建组件和应用程序。从那方面来说，这是一本和其他书一样的技术书。](https://buch.react-js.dev)

更有趣的方面是它是一本技术书的第二种方式:也就是说，它产生的方式。

## 写作阶段

我使用的是新版 gitbook.io 的早期版本，因为它是用来存放技术文章和文本的，比如文档或者...书籍。至少当你看到他们的名字时，你可能会这么想。

事实证明，虽然它确实非常适合以一种非常好和简单的方式编写全面的文档，但它并不真正打算用于编写实际的书籍。在我写这本书的时候，甚至还没有办法将你的 Gitbook 导出为 PDF 或 ePub 之类的任何其他格式(我甚至不确定今天是否有官方的方法)。相反，他们所做的是发布一个设计精美的 HTML 版本，该版本完全以 Markdown 文件的形式存储在 GitHub 上，并做了一些定制的改进。

直到我写了大约 120 页，我才知道把你的 Gitbook 变成一本真正的书有多难，我也不想回到 Google Docs 或 Word 去写，因为它仍然是一本有很多代码示例的技术书，它们在 Word docs 中看起来真的不太好。

## 好玩的部分

所以我做了开发人员有其他更重要的事情要做时会做的事情:我拖延并围绕我的书建立了我自己的工具集和技术栈。

我克隆了我的书的 [GitHub repo。我安装了](https://github.com/manuelbieh/react-lernen)[摊牌](http://showdownjs.com/)和摊牌高亮插件，将 Markdown 源文件转换成 HTML 格式，并附有精美的代码示例。我甚至使用了[更漂亮的](https://prettier.io/)来自动格式化代码块中的代码示例！然后，我将所有转换后的文件合并成一个巨大的 HTML 文件。

然后我将[木偶师](https://github.com/GoogleChrome/puppeteer)添加到堆栈中。这让我可以很容易地将之前转换成 HTML 的 Markdown 文件转换成 PDF。由于我使用 HTML 作为转换的源格式，我能够使用 CSS 来*风格*整本书！Puppeteer 非常擅长将 HTML 文件转换成 PDF 文件，并对 CSS 打印功能提供了相当好的支持(嗯，有时更好，有时不太好)。通过这种方式，我能够对 PDF 进行布局和样式设计(后来我将它发送到了打印店！)和我喜欢的差不多。

对于 ePub 版本，我决定使用神奇的 Pandoc。Pandoc 允许你转换许多不同的文本格式(Docx，HTML，Markdown，...)转换成 ePub 之类的其他格式。为 ePub 编写 CSS 感觉有点像构建电子邮件模板。这确实是你不想定制太多的东西，因为你不能保证你的 ePub 在每个客户端看起来都是你想要的样子。所以我只在必要的时候使用很少的自定义 CSS。

## 须知

整个过程有点棘手，需要一些黑客攻击(一如既往，嗯？)而且它也有一些警告。例如，我无法在 PDF 的目录中添加页码，因为当我生成目录时，我不知道页码，因为它们将在最后的 PDF 转换步骤中由 Puppeteer 自动添加。

CSS [`orphans`](https://developer.mozilla.org/en-US/docs/Web/CSS/orphans) 和 [`widows`](https://developer.mozilla.org/en-US/docs/Web/CSS/widows) 属性似乎也不能正常工作，或者至少不能像我预期的那样工作。而我的印象是， [`break-*`](https://developer.mozilla.org/en-US/docs/Web/CSS/break-after) 属性有时也会引起一些意想不到的行为。不漂亮，但对我来说可以忽略。

不过总的来说，我对结果非常满意:一本书，在我的网络浏览器中编写，在 GitHub 上作为 Markdown 存储，并完全基于网络技术转换成真正的印刷书籍！

代码仍然有点粗糙，我从来没有真正清理过，但是如果你有兴趣复制它，或者甚至想把你自己的 Gitbook 转换成一个漂亮的 PDF 格式，你可以在 GitHub 上找到代码:[https://github.com/manuelbieh/gitbook-pdf-converter](https://github.com/manuelbieh/gitbook-pdf-converter)

这里有一个小小的印象，结果最终是什么样子:
[![](img/4f790b31e33ffdfa8cb2540ab57c4a4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IBOPsuMw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tmuyyk1dc9yb8g08irx8.jpg)