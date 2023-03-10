# 学习编译器如何工作，并陷入困境

> 原文：<https://dev.to/sammyisa/learning-how-compilers-work-and-getting-stuck-17jd>

注意事项:

*   和以前的文章一样，这篇文章不太像是一篇指导性的文章，更像是一个问题+到目前为止我与编译器斗争的有趣叙述。稍后会有更多的指导性文章。
*   这仅与编译器有关，与解释器无关。我还不知道口译员是如何工作的。

## 背景

作为一个计算机科学学生，在大学里上的所有课程中，有些没什么用，有些没什么用，你可能会惊讶地发现，我居然喜欢编程语言和系统软件的课程——这对我来说当然是一个惊喜，因为我不知道我对它们感兴趣！

特别是，我对学习语言是如何工作的很感兴趣。我事先只知道，编程语言被翻译成机器代码，让计算机明白该做什么，因为计算机不懂 [JSFuck](https://en.wikipedia.org/wiki/JSFuck) 。然而，我不知道我们如何从人类可读的编程语言到机器语言，我一度认为这是将西班牙语翻译成英语。在我得知事实并非如此后，我只是简单地认为这是计算机科学魔法的一个黑盒，并没有推动这个主题。

我大学的系统软件课教了我们更多关于黑匣子里面是什么的知识，他们让我们为一种叫做 [PL/0](https://en.wikipedia.org/wiki/PL/0) 的语言的简化版本构建一个编译器。这是我第二次上这门课，当时这门课对我来说很有意思，我要感谢教授。正是在这门课上，我了解了编译语言的步骤(解析器、分析器等等),什么是抽象语法树(AST ),它是编译的中间步骤，是理解语言如何工作以及计算机如何理解语言的一个非常强大的工具。

把你带到这条微博的所有背景故事:

> ![Yehuda Katz 🥨 profile image](img/2eff66bca7ce1ab7efa5531e8db8a693.png)耶胡达·卡兹🥨[@ wycats](https://dev.to/wycats)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)在创作车把之前，我对解析器几乎一无所知。一样的交易。让你的雄心，而不是你现有的技能，成为你的向导。[twitter.com/slicknet/statu…](https://t.co/dVQRg3nXUP)02:14am-2019 年 5 月 22 日尼古拉斯·c·扎卡斯 @slicknet在创建 ESLint 之前，我对解析器几乎一无所知。今天，我知道的比这多一点。不要让你不知道的事情阻止你去尝试。[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1131020431052247043)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1131020431052247043)378[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1131020431052247043)1727

这正好发生在我想更深入地研究开发人员体验工具的时候。与此同时，就像上帝安排的时间一样，我的兄弟与我分享了这个关于学习构建你自己的*任何东西*的报告:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [丹尼斯特凡诺维奇](https://github.com/danistefanovic) / [自建 x](https://github.com/danistefanovic/build-your-own-x)

### 🤓构建您自己的(在此插入技术)

<article class="markdown-body entry-content container-lg" itemprop="text">

**[提交教程](https://github.com/danistefanovic/build-your-own-x/issues/new)**

# [![Build your own X](img/32fbe566679e52c45ba875beb2782113.png)](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/feynman.png)

## 目录

*   [3D 渲染器](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-3d-renderer)
*   [增强现实](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-augmented-reality)
*   [BitTorrent 客户端](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-bittorrent-client)
*   [区块链/加密货币](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-blockchain--cryptocurrency)
*   机器人
*   [命令行工具](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-command-line-tool)
*   [数据库](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-database)
*   [码头工人](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-docker)
*   [仿真器/虚拟机](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-emulator--virtual-machine)
*   [前端框架/库](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-front-end-framework--library)
*   [游戏](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-game)
*   [去](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-git)
*   [网络堆栈](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-network-stack)
*   [神经网络](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-neural-network)
*   [操作系统](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-operating-system)
*   [物理引擎](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-physics-engine)
*   [编程语言](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-programming-language)
*   [正则表达式引擎](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-regex-engine)
*   [搜索引擎](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-search-engine)
*   [外壳](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-shell)
*   [模板引擎](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-template-engine)
*   [文本编辑器](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-text-editor)
*   [视觉识别系统](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-visual-recognition-system)
*   [体素引擎](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-voxel-engine)
*   [网络搜索引擎](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-web-search-engine)
*   [网络服务器](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#build-your-own-web-server)
*   [未分类的](https://raw.githubusercontent.com/danistefanovic/build-your-own-x/master/#uncategorized)

## 教程

#### 打造自己的`3D Renderer`

*   [**C++** : *光线追踪简介:创建 3D 图像的简单方法*](https://www.scratchapixel.com/lessons/3d-basic-rendering/introduction-to-ray-tracing/how-does-it-work)
*   [**c++**:*OpenGL 的工作原理:500 行代码的软件渲染*](https://github.com/ssloy/tinyrenderer/wiki)
*   [**C++** : *沃尔芬斯坦 3D*T5【光线投射引擎】](http://lodev.org/cgtutor/raycasting.html)
*   [**C++** : *物理基础渲染:从理论到实现*](http://www.pbr-book.org/)
*   [**C++** : *栅格化:一个实用的实现*](https://www.scratchapixel.com/lessons/3d-basic-rendering/rasterization-practical-implementation/overview-rasterization-algorithm)
*   [**C#/TypeScript/JavaScript**:*学习如何用 c#、TypeScript 或 JavaScript*](https://www.davrous.com/2013/06/13/tutorial-series-learning-how-to-write-a-3d-soft-engine-from-scratch-in-c-typescript-or-javascript/) 从头开始编写 3D 软引擎
*   [**Java / JavaScript** : *打造自己的 3D 渲染器*](https://avik-das.github.io/build-your-own-raytracer/)
*   [**Java** : *如何创建自己的*](http://blog.rogach.org/2015/08/how-to-create-your-own-simple-3d-render.html) …

</article>

[View on GitHub](https://github.com/danistefanovic/build-your-own-x)

链接到这篇关于用 JavaScript 构建自己的编译器的报道:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ jamiebuilds ](https://github.com/jamiebuilds) / [超级微编译器](https://github.com/jamiebuilds/the-super-tiny-compiler)

### ⛄可能是有史以来最小的编译器

<article class="markdown-body entry-content container-lg" itemprop="text">

[![The Super Tiny Compiler](img/ee9b90cc68a0f42ed837b942dce1fac0.png)](https://raw.githubusercontent.com/jamiebuilds/the-super-tiny-compiler/master/the-super-tiny-compiler.js)

***欢迎来到超级小编译器！**T3】*

这是一个用易于阅读的 JavaScript 编写的现代编译器所有主要部分的超简化示例。

通读引导代码将帮助你了解大多数编译器是如何从头到尾工作的。

### [想跳代码？点击此处](https://raw.githubusercontent.com/jamiebuilds/the-super-tiny-compiler/master/the-super-tiny-compiler.js)

### [你也可以在 Glitch](https://the-super-tiny-compiler.glitch.me/) 上查看

* * *

### 我为什么要在乎？

这很公平，大多数人在日常工作中并不需要考虑编译器。然而，编译器就在你的周围，你使用的大量工具都是基于从编译器那里借来的概念。

### 但是编译器很吓人！

是的，他们是。但这是我们的错(编写编译器的人)，我们把一些相当简单的东西变得如此可怕，以至于大多数人认为它是完全不可接近的东西，只有最书呆子才能理解。

### 好吧，那么在哪里

…</article>

[View on GitHub](https://github.com/jamiebuilds/the-super-tiny-compiler)

来自于@mattsparks 的一篇精彩的文章:

[![mattsparks image](img/b65f666e257aae5b453cbfbbd4341b8a.png)](/mattsparks) [## 🧠学习！5 个 Github Repos，包含大量免费和开源资源

### 马特·斯帕克斯 5 月 29 日 1911 分钟阅读

#webdev #beginners #opensource #programming](/mattsparks/learn-5-github-repos-with-great-free-open-source-resources-38de)

## 当前形势

因此，我开始研究超级小编译器，以了解它是如何工作的，并将其转换为 TypeScript，以向自己证明我了解编译器的不同阶段发生了什么。

我在一个存储库中已经这样做了，它是前面提到的那个的一个分支，你可以在这里找到它:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ SammyIsra ](https://github.com/SammyIsra) / [超级小编译器](https://github.com/SammyIsra/the-super-tiny-compiler)

### ⛄可能是有史以来最小的编译器

<article class="markdown-body entry-content container-lg" itemprop="text">

我的第一次尝试(校外)是学习编译器如何工作，以及什么是 TF AST

这个版本将主要是打字稿，因为这是我正确理解它如何工作的好方法

## 此行下方的原始自述文件

[![The Super Tiny Compiler](img/ee9b90cc68a0f42ed837b942dce1fac0.png)](https://raw.githubusercontent.com/SammyIsra/the-super-tiny-compiler/master/the-super-tiny-compiler.js)

***欢迎来到超级小编译器！**T3】*

这是一个用易于阅读的 JavaScript 编写的现代编译器所有主要部分的超简化示例。

通读引导代码将帮助你了解大多数编译器是如何从头到尾工作的。

### [想跳代码？点击此处](https://raw.githubusercontent.com/SammyIsra/the-super-tiny-compiler/master/the-super-tiny-compiler.js)

### [你也可以在 Glitch](https://the-super-tiny-compiler.glitch.me/) 上查看

* * *

### 我为什么要在乎？

这很公平，大多数人在日常工作中并不需要考虑编译器。然而，编译器就在你的周围，你使用的大量工具都是基于从编译器那里借来的概念。

### 但是编译器很吓人！

是的，他们是。但那是…

</article>

[View on GitHub](https://github.com/SammyIsra/the-super-tiny-compiler)

前两步还不错:

1.  第一步([记号赋予器](https://the-super-tiny-compiler.glitch.me/tokenizer))将起始语言中的输入字符串转化为一系列记号，每个记号被标识为一个括号、数字、字符串或名称。
2.  第二步([解析器](https://the-super-tiny-compiler.glitch.me/parser))导航令牌列表，通过分析令牌列表并确定每个令牌是哪种节点以及后面的令牌是否是子节点来创建一个简单的 AST。

我将在更多的指导性文章中对这个问题进行更深入的讨论，这些文章将附有插图，因为这是我最初理解这些主题的方式。

## 拦截器

步骤#3 和# 4([移动器](https://the-super-tiny-compiler.glitch.me/traverser)和[变压器](https://the-super-tiny-compiler.glitch.me/transformer))是我目前卡住的地方。从它们执行编译过程的下一步的意义上来说，这些步骤似乎是共存的。

然而，当我试图理解我们如何从步骤 2 中获得简单的 AST，并将其转换为代码生成步骤可以用来创建编译器输出的更复杂的 AST，以及如何将这些知识转换为所述步骤创建的类型时，我感到很困惑。

我对编译器这一部分的理解是，转换器接受遍历器和简单 AST，创建一个“访问者”,它指示在简单 AST 中找到特定类型的节点时会发生什么，它输出新的 AST(如代码中所提到的),这是代码生成步骤用来创建输出代码的输入。

所以这是我提出问题和请求帮助的地方，编译器的遍历器和转换器步骤对我来说不是最清楚的主题，所以如果你有任何关于它们的知识或者如果你有和我相似的经历，我很想听听。

* * *

快速笔记。写完这篇文章后，我发现这个视频出自同一作者:[https://www.youtube.com/watch?v=Tar4WgAfMr4](https://www.youtube.com/watch?v=Tar4WgAfMr4)