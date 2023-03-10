# 制作真正的离线第一应用的挑战

> 原文：<https://dev.to/ideagrowr/the-challenges-of-making-a-true-offline-first-app-2p51>

在我们最近的黑客行动中，我们与 Immer 团队一起努力重建 Idea Growr，以添加在线帐户。我们在“离线优先”方面遇到了一些挑战，但找到了一条新的道路。

## 使其在线，同时保持其离线

在之前的文章中，我已经写了关于应用程序的概念方面。现在我们可以开始实际构建一个改进的应用程序了。

从技术角度来说，目前的离线 android 应用程序(从现在起我将调用经典)相当简单。您可以存储文本、图像和音频，并能够检索和编辑它们。

[![](img/3d0aabdf283721aed43371f61e2b4f17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--035VMjeT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/265yq4ihgdxrn1q827gu.png)

[中篇](https://medium.com/idea-growr/rebuilding-idea-growr-offline-first-using-react-native-graphql-docker-during-our-hackaton-576b6f7a8b90)在 app 架构、团队成员插图等方面更为精细。在这里，我将重点放在技术方面；先下线。

# 下线第一，还是第二？

一种新的思维方式开始在 web 和移动开发场景中出现，称为离线优先。

[https://offlinefirst.org](https://offlinefirst.org)

> 我们生活在一个断开连接和电池供电的世界，但我们的技术和最佳实践是从始终连接和稳定供电的过去遗留下来的。
> 
> 离线能力是现代渐进式 Web 应用程序的一个关键特征。离线优先思维必须借鉴并推进我们所看到的反应灵敏和移动优先思维的工作。

仔细观察，他们真的在谈论我所说的线下第二个。一个处理连接中断的网络应用程序应该在**首先上线**。

我对离线优先的定义是，你**先离线**构建它。没有互联网，没有在线账户。然后，当整个应用程序工作时，在这段代码的基础上添加**在线功能第二个**。

创建一个离线的第一应用程序有一个架构上的挑战；你如何同步数据？我将尽可能以非技术性的方式描述两种不同的策略。

## 策略 1:使用解析器缓存查询&

一种方法是，将在线数据库视为唯一的事实。您可以向该数据库提问(查询)，当连接断开时，您可以在本地记住这些查询的答案。您使用缓存存储来保存对数据库的回答。

我们使用 Apollo 进行数据管理，并且必须小心如何使用这个缓存。当您离线时，问题的最后一个答案并不总是正确的。

[![](img/6cb4cc30b61c4b3a904a36f92b81ac66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QRoUqhxl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9s0ezamfycact8r9f1jw.png)

为了处理这个问题，您必须为每个查询手工编写一些代码。你编写解析器来确保正确的事情发生。对话变成了这样:

[![](img/1f56fb0fa74f6f2ce0598a3ef88fccd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--77iDDzo0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/byl5ezb0k0tzmnebdook.png)

在此对话之后，服务器可以重新联机，查询答案的本地存储将用于将数据同步到服务器。

这种方法令人讨厌的地方似乎是，您必须为每个查询创建解析器，然后慢慢地开始以解析器的形式构建本地数据库。充满难以管理的纠结的本地数据库。

我最大的希望🙏我很无知，有人评论说我们只需要做 X 和 Y 来得到一个干净的可管理的架构。

## 策略 2:本地数据库&将差异传达给服务器

虽然第一种策略依赖于向在线服务器提供对数据库的本地理解的变化列表，但是第二种策略是使用实际的本地数据库。

优势之一是它简化了离线行为。该应用程序只是使用一种常规的方式与本地数据库(SQL 查询)交谈。然后，在本地数据库和在线服务器之间放置一个通用软件，我称之为同步程序。它会跟踪不同步的内容，并在需要时进行更新。您编写什么 SQL 查询来对本地数据库进行更改并不重要，所以现在解析器、同步器只需查看数据库的状态，看看有什么不同。

[![](img/d44a44730d3b87ae8d579e17393d5dee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LQOj5_JT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nsu14zhvuhuiqklg4c24.png)

这种解决方案让我想起了像 Git 这样的版本控制是如何用于代码管理的。Git 是一个跟踪你的代码的工具，它可以确保你的电脑上有一个可以离线使用的副本，而且还可以与服务器上的代码同步。

你必须从本地开始编码，然后再添加 Git。同样，我想先用本地数据库创建一个应用程序，然后再添加一个同步程序。

[..在这篇中型文章中，有一些潜在的前进道路..](https://medium.com/idea-growr/rebuilding-idea-growr-offline-first-using-react-native-graphql-docker-during-our-hackaton-576b6f7a8b90)

## 期望的过程

我们还能做得更好吗？我们似乎试图一次解决太多问题。理想的过程保持动力。在每一步，我们的团队都取得了明显的进步，我们可以不断学习和提高。

事后看来，我应该让团队开发一个离线应用。我们做了一个简短的评估，这是我们的新路线图:

*   **仅离线**(简单 Sqlite 数据库)尽快在 Apple store 中获得 React 原生应用程序，以便我们可以开始学习和改进。这意味着我们开始只在设备上存储想法。虽然经典的 Android 应用程序也是离线的，但我们还没有 iOs 应用程序，所以我们可以先推出它。
*   备份完整的数据库如果他们所有的想法都有一个简单的备份功能，这将对他们有很大的帮助。现在有一个非常繁琐的导出/导入功能。一个像样的备份功能将让我们有机会了解我们的用户在这方面的需求，并处理服务器端，分析等。
*   一旦内核开始工作，我们就可以在此基础上构建智能同步功能。甚至在此之前，我们现在就可以在我们的一个文本应用程序概念验证中开始试验。
*   然后我们沿着之前文章中讨论的道路前进。从团队合作开始。

## 好了

我们玩得很开心，进步很大，前进了两步，后退了一步。我们调整了我们的计划，现在集中精力尽快将应用程序带到苹果商店。

如果你对我们*线下第一次挑战*有什么建议，请在评论中分享你的智慧。我会不断更新这篇文章，吸收你和其他人的新见解。

在我们的推特[@ idea grower](https://dev.to/ideagrowr)和这里的[媒体](https://medium.com/idea-growr)上了解更多我们的进展