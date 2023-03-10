# 用(几乎)零编码技能编写一个 Java 游戏

> 原文：<https://dev.to/selawsky/code-a-java-game-with-almost-zero-coding-skills-1fmh>

[![Code a Java Game with Zero Coding Skills](img/f405566ae06bd43a07b7dca79e3ac9e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rx6HiZI0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6y7j5447pgxn12d5zmb7.jpg)

每个人，嗯，几乎每个人，都喜欢电子游戏。因此，即使你不打算成为一名专业的游戏开发人员或者只是不确定，通过实践学习一门编程语言并创建自己的视频游戏项目也是一个很好的主意。此外，这可能是你学习的好动力。

本文面向所有潜在的和初学 Java 的学生，以及所有想创建自己的 Java 游戏项目，但不知道从哪里开始以及如何以最佳方式学习这门语言的人。

### Java 对 GameDev 不好吗？..神话与现实

[![GameDev](img/8a866167574a94ff3565b3cd895844dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bGndNLCK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7sd9zw2wrtph3zoaaocv.gif)

如果你阅读一些关于游戏开发的论坛和文章，尤其是那些已经存在一段时间的，你可能会有这样的印象，Java 不是游戏开发的好选择。但是，这种观点有些过时和不准确。电子游戏世界非常多样化。通常，您可以将游戏分为以下几类:

**“大型”游戏。**比如 3D-shooters，大型动作冒险/动作 RPG。这一类也包括 AAA 级项目。通常是为大众受众设计的高预算游戏如《红色死亡救赎 2》、《刺客信条起源》等。这类游戏一般都是用 C++写的，附带游戏引擎。

由于 JVM 的特性，Java 在这种游戏开发中是一个罕见的客人。更准确地说，它可以用于创建后端部件。

[![Assasin](img/58a5f77fff3bd67fc8acfd18512828c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dNZ3wtW3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackernoon.com/photos/qeIv6rNygoYrUXBwG7uA2lPpLSj2-je3b731z5)

**独立游戏。**一个游戏玩家对独立项目嗤之以鼻，另一个游戏玩家对独立项目充满热情。独立游戏是指由一个小团队甚至一个开发者创作的独立游戏。

独立电影中有许多业余项目，其中一些并不为创作者赚钱。独立游戏更多的是原创想法和游戏性，好的剧本和主观视觉，而不是性能和高端图形。然而，这样的独立项目从市场中爆发出来，变得非常流行。这种游戏的一个很好的例子是《我的世界》，最初是由一个人，马库斯·佩尔松创造的。截至 2019 年 5 月，《我的世界》已售出超过 1.76 亿册。这使得它成为有史以来最畅销的视频游戏之一。2014 年，微软以 25 亿美元收购了《我的世界》和 Mojang。顺便说一下，Markus Persson 是一名 Java 开发人员，所以他用这种语言编写了他的杰出成果。所以，没错，Java 确实非常适合独立项目。

[![Minecraft](img/ecc9fc481d7d9c1be7218d9a07adbff8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_eZMvwYi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m27s69q66otrzq3nihzx.gif)

**手机游戏。**这个巨大的市场随着智能手机的数量和性能不断增长。几乎每个人都在他们的移动设备上玩过游戏。它可能是 2048，一个旧游戏的移动翻拍或一个有原创想法的游戏。Java 对手机游戏开发好吗？答案是肯定的。Android 智能手机的市场份额现在占了 80%。你可能会认为 Java 是 Android 的原生语言，所以确实有很多手机游戏是用这种编程语言编写的。

**结论。** Java 对于手机游戏和独立项目开发(这些市场有些交叉)以及高负载网游的服务器部分的开发都有好处。现在是独立成长时间，如果你对这类游戏感兴趣，可以在 Java 学习期间尝试制作一些。

而且，Java 是一种通用语言。它用于服务器端企业开发、大数据、移动和 Web 应用、交易应用、嵌入式空间等等。Java 开发人员可以自由转换他们的编程角色。因此，与其他编程语言相比，Java 给了你更多选择的自由。

如果你决定成为一个大的游戏开发者，那么 C++和一些游戏引擎如 Unity 和 Unreal 是你学习的选择。

### 一个编程新手可以自己创作游戏吗？怎么做？

事实上，即使是最简单的视频游戏也不太容易编写，尤其是对于一个没有编码经验的人来说。幸运的是，我们生活在开放的信息时代，所以我们可以找到不同的循序渐进的指示和不同水平的课程。在这一段中，我们将讨论新手用 Java 创建游戏的三种选择。

### 案例 1。从头开始使用一步一步的指示。CodeGym，[游戏板块](https://codegym.cc/projects/games)

[![Codegym](img/ef068cf0e3b433ba7c3c8e83ce7657cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VV15jXBx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ktvduc5gicqin20i41a2.png)

这个特性适合早期的初学者。你可以用非常基础的 Java 知识创建你的 3 个经典游戏版本(现在，还有 3 个在开发中)。CodeGym 上的游戏部分是主课程的一部分，对所有用户都是免费的，不仅仅是 CodeGym 的学生。

CodeGym 游戏部分让您创建自己版本的传奇视频游戏，如扫雷，2048 或蛇(太空入侵者，月球着陆器和赛车正在开发中)。

每个游戏都是一个任务，包含 24 个子任务和逐步说明。学生获得子任务和提示，编写代码，然后由自动 CodeGym 系统检查。如果代码以正确的方式运行，学生将进入下一步。所有的代码都直接写在 CodeGym 网站上，或者通过你可以在 CodeGym 上得到的特殊插件写在 IntelliJ IDEA 中。

[![CodeGym2](img/cfa7596f90a28f1679bfd910197434ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D8KXWZHM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/052e72197v969zg1s7lc.png)

完成最后一步，你就有了一个现成的游戏，你可以在网站上发布，根据你的创意进行定制，并与你的朋友分享。这些游戏使用简单的 CodeGym 游戏引擎，其结构你可以从本文中了解。

为这些游戏编码你应该知道什么？这是第一批 Java 程序的水平:关于类和对象、基本原始数据类型、字符串、循环和分支的初始知识，以及关于数组和数组列表结构的初始知识。CodeGym 的作者建议完成[课程](https://codegym.cc/quests)的前 5 个级别来获得这样的知识。如果你感到不够自信，这里有一个[有用的理论](https://codegym.cc/groups/posts/140-games-section-on-codegym-useful-theory)可以帮助你解决问题。如果你知道 Java Core 但从未尝试过制作游戏，CodeGym 游戏对你来说仍然很有趣，尤其是在他们批准后有机会修改和定制你的标准解决方案。

付费: [CodeGym Games](https://codegym.cc/projects/games) 免费使用。

### 案例二。对于热爱《我的世界》的新手到中级 Java 学生来说

[![Minecraft](img/93456999f1db76715e83596336616282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GbyqjUz6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/31xi7n5caqhn2oxtbjcr.png)

如果你喜欢《我的世界》，尝试用 Java 做一些修改是一个很好的主意。大多数 mod 在游戏中增加了一些内容来改变游戏性，或者给玩家更多与《我的世界》世界互动的选择。例如，你可以编写一个新的更精确的弓或者类似的东西。有些人就在《我的世界》制作[迷你游戏。](https://education.minecraft.net/lessons/make-a-game/)

即使你不是这个游戏的狂热爱好者，对每个新手来说，使用反编译的代码，试图理解它并为你改变它也是有益的。稍后你可以用你自己的模式玩《我的世界》。

要开始修改，你应该安装你的 IDE 和 JDK 以及一个修改工具。Forge 是最流行的 Java 建模工具之一。有无数的 Java《我的世界》建模教程和课程。

以下是其中的一些:

*   [2019 年用 Java 进行《我的世界》改造的终极指南](https://codakid.com/guide-to-minecraft-modding-with-java/)
*   [《我的世界》改装教程 1.12](https://www.youtube.com/results?search_query=minecraft+modding+tutorial+1.12+) (视频)
*   [改装教程 1.14](https://www.youtube.com/playlist?list=PLmaTwVFUUXiBKYYSyrv_uPPoPZtEsCBVJ) (视频)

### 用 Java 修改《我的世界》应该知道什么？

这取决于你想做的模式的复杂程度。你至少应该知道 Java 类和对象，数据结构的基础，然而，如果你有或多或少的 OOP 使用、线程和流的介绍和 Java 集合的扎实技能，那会更有用。

付款:通常你应该买你自己的《我的世界》版本。如果你不想自学 modding，在网上很容易找到一些付费课程。

### 案例三。我有扎实的 Java 核心知识，我已经为自己的游戏项目做好了准备

[![Java Core](img/867bfd68c158fda26f82d939fe60f2b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o14F8l5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ifgkfu1wlpwlmd67v700.gif)

当然这个案子不是关于完全的新手。在这一点上，我们会告诉你一个决定创建游戏项目的 Java 新手程序员应该注意什么。

你对 indy 跨平台或移动项目有想法吗？也许你知道如何创建你自己版本的流行游戏？嗯，如果你只知道 Java Core，对游戏开发一无所知，你该怎么办？用原始的 Java 编写一个游戏是非常困难的，但是有非常有趣和有用的框架和库来帮助你实现你的想法。

这个游戏由什么组成？

*   制图法
*   物理学(游戏中物体互动的方式)
*   AI(它不在每个游戏中出现，但经常出现)
*   图像使用者界面
*   声音

OpenGL 是用于渲染 2D 和 3D 矢量图形的跨平台应用编程接口(API)。在 Java 中，你可以用 OpenGL 函数搭配一些库——[Java 3d](http://www.java3d.org/tutorial.html)、 [JOGL](https://jogamp.org/jogl/www/) (Java OpenGL)和 [LWJGL](https://www.lwjgl.org/) (轻量级 Java 游戏库)。它们都是跨平台、免费和开源的。

最受欢迎的是最后一个，LWJGL。它是许多其他游戏引擎和库的核心技术。LWJGL 让您可以访问 OpenGL 图形库，并包含创建 2D 和 3D 游戏所需的一切。LWJGL 还支持用于创建音乐和声音效果的强大的跨平台 OpenAL，并支持 OpenCL，这是一种跨平台并行编程的标准。

[libGDX](https://libgdx.badlogicgames.com/) 是一个面向跨平台游戏的 LWJGL 框架。它包含了一个有用的[盒子 2D 引擎](https://github.com/libgdx/libgdx/wiki/Box2d)，可以让你创建一个游戏物理，许多图形类，处理音频的工具。

libGDX 由几个模块组成，所以你可以用它来创建基于人工智能的角色，控制灯光行为，声音和物理。

使用 libGDX 和 box2d，你可以为不同的平台开发游戏，比如 Android、iOS、Windows、Linux、macOS 和 Web。你不需要为它们中的每一个重新创建代码，只需写一次，由于跨平台，它就可以在每个系统上工作。

因此，对于初学 Java 游戏开发的人来说，学习 libGDX 是一个好主意。

### 还有什么？

**游戏开发不仅仅是明确的编程。**如果您打算独自完成项目，您还应该:

制作一些像素艺术，纹理，角色的精灵。你需要一个图像编辑器。比如[品他](https://pinta-project.com/pintaproject/pinta/releases)或者[Paint.Net](http://www.getpaint.net/index.html)。

**设计关卡地图。要创建地图，您需要一个 tileset。您可以在图形编辑器中创建它。要用平铺创建地图，你可以使用地图编辑器[平铺](http://www.mapeditor.org/download.html)。**

**创建纹理图谱。纹理地图是一个包含我们所有精灵的大图像。Atlas 无疑加快了开发过程。你可以使用免费的[雪碧打包机](https://www.codeandweb.com/free-sprite-sheet-packer)来完成这项任务。**

**创建图形界面。例如，使用 Overlap2D
有许多使用 libGDX 的好教程。这里有一个[官方网站](https://libgdx.badlogicgames.com/documentation/gettingstarted/Creating%20Projects.html)的链接。**

### 结论

无论你以后会成为一名游戏开发人员，还是更喜欢编程的其他分支，创建自己的游戏对你的 Java 学习绝对是大有裨益的。编写一个游戏，你参与了一个典型软件开发项目从开始到发布的所有过程。你看到了你的工作成果，并可能向你的朋友展示，甚至在某处发布(例如 Google Play)。

即使你是一个绝对的初学者，你也可以尝试使用一些 web 教程和工具来创建一个视频游戏，例如 [CodeGym 游戏部分](https://codegym.cc/projects/games)。

你也可以尝试创建一些现有游戏的模式，《我的世界》(Java 版)和 [Forge](https://files.minecraftforge.net/maven/net/minecraftforge/forge/index_1.12.2.html) 非常适合这个。

如果你对 Java Core 足够了解，那么对于你的第一个游戏项目来说， [libGDX](https://libgdx.badlogicgames.com/) framework 是一个很好的学习和使用选择。

[![Good luck](img/567a07f6e2c993e3fd9439145eacc08b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BUgqX0CG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7denubwib7s20hn4kaq8.gif)

祝你游戏好运！

这篇文章最初发表在 [Hackernoon](https://hackernoon.com/code-a-java-game-with-almost-zero-coding-skills-z442w31dh) 上。