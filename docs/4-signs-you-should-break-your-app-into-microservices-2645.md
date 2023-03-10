# 你应该将你的应用分成微服务的 4 个标志

> 原文：<https://dev.to/loujaybee/4-signs-you-should-break-your-app-into-microservices-2645>

微服务在过去的几年里被谈论了很多。普遍接受的做法是，应用程序在分解为微服务之前应该从整体应用程序开始。事实上，这正是我们如何使[分裂](http://splitoo.com)。

2019 年早些时候[我承诺改进 Splitoo](https://dev.to/loujaybee/2018-a-year-in-review-239i-temp-slug-8345926) 的一些技术方面。其中一个方面可以称为微服务。在 Splitoo，由于我们是一个小团队，我们做出了一些相当直观的决定，根据一些迹象表明应用程序需要微服务，将应用程序分解为微服务。

[![](img/e4b4882b89c65ee86459e9cb99d50d0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UPTmY9Lp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.thedevcoach.co.uk/wp-content/uploads/2019/08/sam-balye-k5RD4dl8Y1o-unsplash.jpg%3Ffit%3D760%252C507%26ssl%3D1)

我们意识到，我们分解应用程序的决定来自于更直观地了解应用程序变得太大的迹象，并且可以从分解为微服务中受益。这就是我们今天要讨论的内容。

到本文结束时，您应该知道应用程序可以从微服务方法中获益的四个迹象。

## 什么是微服务？

在我们讨论如何判断您的应用是否应该考虑微服务之前，让我们先来看一个定义。

社区对微服务的定义存在分歧。但是让我们定义一个我们今天可以使用的。

> 微服务是应用程序的单个(独立部署的)部分，通常通过网络边界与其他服务进行通信。

有了我们的定义，让我们来详细了解您的应用可能从微服务架构中受益的迹象。

## 1。您希望有单独的部署

[![](img/e1a6098d2e739bb419ab2a0ec01e3949.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y3pb4w9V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.thedevcoach.co.uk/wp-content/uploads/2019/07/awsinfra.png%3Fresize%3D760%252C527%26ssl%3D1)

注意到应用可以从微服务中获益的一个主要方式是，您开始期望独立的部署过程。

但是，为什么独立部署可能是可取的呢？

我们可能需要单独部署过程的一个原因是，我们应用程序的不同部分或多或少是任务关键型的。拆分我们的部署过程可能对我们有好处，这样我们可以在一些领域更具侵略性和实验性，而在其他领域更谨慎和保留。

举个例子，因为 Splitoo 是一个金融应用程序，所以不可避免地会有一些部分比其他部分更重要。

关键服务是那些覆盖我们核心客户旅程的服务，是那些如果停机或不稳定会对我们的用户群造成巨大影响的服务。如果您注意到您的应用有这些不同的领域，那么是时候考虑微服务了。

## 2。您的自动化测试正在变得复杂

[![](img/b8ef45007d66fe2332b1a7705500e6d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uzfxtNk9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.thedevcoach.co.uk/wp-content/uploads/2019/08/john-barkiple-l090uFWoPaI-unsplash.jpg%3Fresize%3D760%252C507%26ssl%3D1)

在大型单片应用程序中，测试变得复杂。更多的代码意味着更难发现应用程序没有被充分测试的地方。不仅如此，耦合到部署中的应用程序注定会像其最慢的测试一样部署缓慢。所以这是不稳定的测试覆盖率和运行缓慢的测试，不是很好。此时，您可能开始考虑分解应用程序，以便获得更好的测试性能。

举个例子，Splitoo 有一个核心的 web 应用程序——很复杂。核心应用程序具有登录、状态和复杂的逻辑。如果我们将核心应用程序的逻辑与一些基本的启动页面进行比较，测试需求会有很大的不同。测试启动页面很简单，没有登录状态，没有应用程序状态。而应用程序的核心要复杂得多。没有理由等待核心应用程序测试运行来发布我们的 splash 页面。

也许是时候考虑微服务了？

## 3。不同的部分需要不同的技术

[![](img/e73ccd5b09908c7c5ee9dd9774e3dd09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jNLJ9kZD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.thedevcoach.co.uk/wp-content/uploads/2019/07/cloudnative.jpg%3Fresize%3D749%252C296%26ssl%3D1)

整体架构可能会被单一技术所束缚。但是有些问题可能需要定制技术。

也许你想做一些数据繁重的处理，这需要使用 Python 的数据库，或者大量的计算资源？您不希望您的请求服务 API 与资源饥渴的数据处理任务混杂在一起，这些任务可以在几个小时之内或在更便宜的机器上运行。

将应用分解为微服务，可以让您灵活地选择不同的应用甚至基础架构来构建您的解决方案。如果您发现应用的不同部分有不同的技术需求，那么是时候考虑微服务了。

## 4。您希望更容易地重用组件

[![](img/d6105f00c49f7b315ff61cc4cc91c94f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CikRPAKJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.thedevcoach.co.uk/wp-content/uploads/2019/08/rick-mason-2FaCKyEEtis-unsplash.jpg%3Fresize%3D760%252C570%26ssl%3D1)

许多应用程序的部分是可重用的。但是，当然，重用是与魔鬼共舞。你可能会花很多时间抽象出应用程序中有用的部分，结果却发现:没有人使用你的“可重用”组件。或者，由于抽象，原始组件变得如此复杂。

更实用的方法是构建一些有用的东西，然后找出共同点。这些常见组件可能是微服务的良好候选。

举最后一个例子，例如，在 Splitoo 中，我们在入职时会发送大量电子邮件。但是发送电子邮件的过程并不独特。因此，我们将电子邮件发送逻辑分解到一个独立的微服务中，可以轻松重用。

## 你能发现这些迹象吗？

今天到此结束！快速浏览一些你的应用程序可能崩溃的迹象。这些并不是导致应用崩溃的所有迹象，所以我很想知道您还遇到了哪些迹象和症状。

考虑到这些，您可以开始建立自己的直觉，看看应用程序何时可以从分解为微服务中受益！

***你能看到这些(或其他！)你目前正在开发的应用程序中的信号？*T3】**

* * *

你应该把你的应用分成微服务的 4 个标志的帖子首先出现在[的开发者蔻驰](https://www.thedevcoach.co.uk)上。

像您一样加入云原生软件工程师社区。了解最新的云技术，学习核心云工程主题的基本技能，如:Docker。无服务器和 Linux，等等。

[加入我们](https://thedevcoach.co.uk/newsletter)并取得成功！