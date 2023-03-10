# 你的文件目录

> 原文：<https://dev.to/amitnovick/a-catalog-of-your-files-2nd7>

我的生活围绕着数字文件。对我来说重要的每一项数据都以文件的形式存在于我的电脑中:

*   📝我一整天都记的笔记
*   🧾发票和定期财务报告
*   🌐网站地址的集合
*   📚一批(电子)书
*   🎵音频文件(音乐、播客)的集合

文件已经成为我创造性工作和组织能力不可或缺的一部分，现在我已经收集了相当多的文件。

但是这篇文章并不是专门针对我的，因为我肯定不是唯一一个管理大量文件的人。因此，如果你在电脑上有大量对你很重要的文件，那么这篇文章绝对适合你！

# 我遇到的问题

随着时间的推移，我的文件越来越多，我尝试用我电脑上的文件和文件夹管理工具来更好地组织我不断增长的收藏。

我的结果如下:如果你有兴趣从你的物理世界复制一个现有的文件和文件夹系统，那么数字文件和文件夹工具(Windows 文件资源管理器，Mac Finder)可能是你能得到的最好的工具，因为它们代表物理对象(🖹📁)，仅限数字格式。

但是对于像我一样想要完全数字化的人来说，我认为文件和文件夹系统在可用性上不如我将在这篇文章中介绍的略有不同的系统。

在文件和文件夹系统中，文件是内容的一个单元，而文件夹是建立类别层次结构的基础。

将文件放在文件夹中意味着“此内容属于此类别”。由于文件夹可以放在其他文件夹中，这允许更精细的层次结构。事实上，将一个文件夹放在另一个文件夹中意味着“这个狭窄的类别是那个更大类别的一部分”。

这意味着在文件和文件夹层次结构之间创建关联涉及识别单个合适的文件夹并将文件插入其中。

但是如果没有一个合适的文件夹会怎么样呢？如果我们想通过两个不同的文件夹访问同一个文件，该怎么办？🤔文件和文件夹系统迫使我们在多个同样糟糕的解决方案中做出选择:

1.  决定哪个文件夹最有意义，并尝试忘记与其他文件夹的关联(👎当您稍后在忽略的文件夹下找不到这个文件时，您会感到失望)
2.  复制文件并将其放在两个文件夹下(👎只要您对其中一个文件进行了修改，这两个文件就会失去同步)
3.  将第一文件夹的所有文件与第二文件夹的所有文件合并(👎并且失去了两个文件夹提供的细粒度分类)
4.  思考你的层级结构一会儿，并作出重大重组，使一切有意义的新文件在里面 Again™️。

公平地说，最后一个解决方案并不完全糟糕，因为层次结构经常需要重新构建，以便在添加新内容时仍然可用。然而，我发现它太严格了，因为它迫使我预先进行重构，这很不方便。我对此的假设是，我们的等级心理模型需要时间来适应每一次发生的重构，所以理想情况下，我们应该推迟它，直到我们的大脑完全接受它。

# 我的解决方案

我建议使用一个与文件和文件夹稍微不同的系统。

这个系统提供了多对多的关系，而不是文件夹到文件的一对多关系。

<figure>

[![Filesystem vs. Catalog: Comparison Diagram](img/72e9c815770fe4808dff2466b23dfb02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RGcqo-Wo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xle535nkmfbxk1t2dwc5.png)

<figcaption>Left: traditional filesystem. Right: proposed system</figcaption>

</figure>

在上图中，左边的图是传统的文件系统:文件依赖于文件夹层次结构，并且只能连接到一个文件夹。

相比之下，右边的图是我提出的层次结构，其中文件完全独立于类别层次结构，并且可以连接到多个类别(或者没有！).

在现实世界中，这是不可能实现的，因为一个文件在任何给定时间只能包含在一个父文件夹中，但在计算机中🤖不必受限于物理世界中的文件夹，可以通过编程来处理具有多个父文件夹或类别的文件。

但是我在这里不仅仅是要告诉你计算机**可以**使用这个建议的系统，而且**我已经成功地实现了这个系统，并且每天都用它**来组织我的文件。

我将这个程序命名为 **Catalog** ,因为这个想法很大程度上是每个用户都是一个*编目员*,并被赋予通过给内容分配类别来对其内容进行分类的权力。

Catalog 很容易上手，文件和文件夹工具的用户应该会发现它使用起来很直观。👍例如，Catalog 有一个用于导航类别层次结构的界面，该界面与您已经用于导航文件夹层次结构的界面惊人地相似:

<figure>

[![Catalog: navigating the categories hierarchy](img/1f9339829c320a534f80138dd2aeb630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L3DsMoSq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vuyl55hgc7t0riiuq87h.png)

<figcaption>Catalog: navigating the categories hierarchy</figcaption>

</figure>

简而言之，Catalog 是我利用业余时间维护的一款开源软件，面向所有平台的用户: **Windows、Mac、Linux** 。100% **免费**，不需要注册。目录也不需要互联网连接，这意味着无论你去哪里，它都可以在你的电脑上运行。

欢迎你来试试 Catalog:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿米托维克](https://github.com/amitnovick) / [目录](https://github.com/amitnovick/catalog)

### 一个桌面文件管理应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">[![Catalog Logo](img/ee802ae281080a8930d3aa5883bef0fa.png)](https://github.com/amitnovick/catalog/blob/master/docs/horizontal-logo.png?raw=true)

> 在图书馆和信息科学中，编目是创建表示信息资源(如书籍、录音、活动图像等)的元数据的过程。
> 
> -维基百科，免费的百科全书

Catalog 是一个免费的开源文件管理应用程序。它通过向文件添加类别来帮助组织本地驱动器上的文件。用户获得相同的用户界面来管理不同平台上的文件。目录与 Windows、Linux 和 Mac 兼容。该应用程序既不需要互联网连接，也不需要用户注册就可以在桌面设备上运行。

[![Catalog App Screenshot](img/84587083d7662a78aa9827475f46a971.png)](https://raw.githubusercontent.com/amitnovick/catalog/master/docs/app-screenshot.png)

## 下载

### 桌面应用程序

| 操作系统 | [计] 下载 |
| --- | --- |
| Windows 操作系统 | [![Get it on Windows](img/78a49b962ca631e4e5c38e883e9e78d3.png)](https://github.com/amitnovick/catalog/releases/download/v0.6.31/catalog-setup-0.6.31.exe) |
| 马科斯 | [![Get it on macOS](img/3e3963fd32c62130aa7df0abff8ff9ef.png)](https://github.com/amitnovick/catalog/releases/download/v0.6.31/Catalog-0.6.31.dmg) |
| Linux 操作系统 | [![Get it on Linux](img/669d747e405a9d6bef23731954d36eaf.png)](https://github.com/amitnovick/catalog/releases/download/v0.6.31/catalog-0.6.31.AppImage) |

### Web Clipper 浏览器扩展

Web Clipper 浏览器扩展是桌面应用程序的一个**可选**伴侣，它提供了从网站捕捉屏幕截图以供以后参考的能力。

| 浏览器 | [计] 下载 |
| --- | --- |
| 火狐浏览器 | [![Get extension for Firefox](img/64599b739067a71cc186f99cbd2cf978.png)](https://addons.mozilla.org/en-US/firefox/addon/catalog-web-clipper/) |
| 铬 | [![Get extension for Google Chrome](img/8c31205be8db53ef31c3134b112ccace.png)](https://chrome.google.com/webstore/detail/catalog-web-clipper/oocdimepfbgcmbokkmfcpjmglbcclnlj) |

## 动机

由于文件夹概念本身的限制，使用文件夹的数字文件组织是低效的。目录提供了另一种组织文件的方法，使用…

</article>

[View on GitHub](https://github.com/amitnovick/catalog)

如果你有任何问题，请在下面评论或者更好的方式告诉我:在 GitHub 上打开一个问题。如果你是⭐️或❤️.，我将不胜感激
感谢您的阅读！