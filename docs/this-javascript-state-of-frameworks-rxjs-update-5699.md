# 这个。JavaScript:框架状态-RxJS 更新

> 原文：<https://dev.to/thisdotmedia/this-javascript-state-of-frameworks-rxjs-update-5699>

由这个主持。JavaScript，这是一个在线活动，开发者可以从中了解 JavaScript、框架和库的最新消息，涵盖了框架领域的所有突发新闻。

2 月 19 日，State of Frameworks 的演讲者，包括许多来自开发世界的最优秀和最聪明的人，给了我们关于所有框架的更新。

下面是[本·莱什](https://twitter.com/BenLesh)、 [RxJS 负责人](http://reactivex.io/)对[即将在 RxJS 发生的事情的一些看法。](https://www.youtube.com/watch?v=DFF9eOlTWzY)

## 本莱什— RxJS 领衔，角队—[@本莱什](https://dev.to/benlesh)

## 6 . 4 . 0 中的 Bug 修复

RxJS 6.4.0 最近发布了。许多变化都是小的错误修复，但也有一些重要的变化。其中一些与打字稿有关。

RxJS 6 一直以 TypeScript 2.8 及更高版本为目标。然而，这是 RxJS 开发者第一次使用特定的 2.8 特性。正在使用 2.7 的用户可能遇到了麻烦，可能需要升级 TypeScript。

shareReplay 功能的用户曾经经历过计数功能的一个 bug，每个人都可以取消订阅，但源会继续运行。RxJS 中对 shareReplay 的新更新提供了选择退出的能力。现在，如果每个人都取消订阅，它会取消订阅源。

对于 RxJS 的新版本，我们使用了 TypeScript 2.8 中的一个特性——条件类型——它提供了一种从另一个泛型类型中提取一个泛型类型的方法。这可以用在各种场合，为 Redux Observable 的用户解决以前的问题。

此外，用户长期以来一直要求 takeWhile inclusive 选项，现在该选项已经可用。

## 给未来的教训

RxJS 核心团队从最后一轮变更和更新中学到了一些重要的经验。

RxJS 7 将移除已弃用的 API，并更新类型以支持 TypeScript 3.0 及更高版本。这使得 RxJS 在迁移到较小版本的 TypeScript 时处于有利地位。

RxJS 的较小版本将是 Rx 8，而 RxJS 7 将在类型上有更多改进。超过 60%的 RxJS 用户使用 TypeScript，所以正确使用它是至关重要的——有时在这样功能的库中这是一个困难。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。