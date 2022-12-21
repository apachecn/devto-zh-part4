# 《烬时报》-第 115 期

> 原文：<https://dev.to/embertimes/the-ember-times-issue-no-115-l7n>

👋恩伯诺斯。本周:见`@model`获取路线模板🆕，分享你对 Apple Music 成为 Ember 应用程序的看法🍎，有助于八分所有的事情📝一个设计师的&开发者过渡到⚛️➡️烬的故事🐹，并庆祝 Ember Sinon QUnit 的 4.0 版本🎉！

* * *

## [遇见`@model`获取路线模板🆕](https://github.com/emberjs/rfcs/blob/ac27abbd1729cd61b67465eee4c8096bb47501da/text/0523-model-argument-for-route-templates.md)

从 Ember Octane (3.14)开始，您可以在 route 模板中使用一个名为、`@model`的**参数来引用来自`model()`钩子的数据。[余烬辛烷指南](https://octane-guides-preview.emberjs.com/)已经[更新](https://github.com/ember-learn/guides-source/pull/1044)以显示`@model`的用途。** 

```
<h2>List of Scientists</h2>

<ul>
  {{#each @model as |scientist|}}
    <li>{{scientist}}</li>
  {{/each}}
</ul> 
```

[`@model`](https://github.com/emberjs/rfcs/blob/ac27abbd1729cd61b67465eee4c8096bb47501da/text/0523-model-argument-for-route-templates.md)的推出是为了帮助新开发者快速学习模板。通过避免`this.model`，他们不需要理解另一个概念——控制器——来构建他们的第一个 Octane 应用。通过使用一个命名参数，我们还可以强化这样一个想法，即`@`是用来表示“作为参数传递”给模板的东西。

你可以现在就在金丝雀上[试用`@model`，或者等待下周的 3.14 测试版！](https://github.com/emberjs/ember.js/pull/18363/files)

* * *

## [Apple Music 推出 Ember 应用🍎](https://discuss.emberjs.com/t/apple-music-beta-is-an-ember-app/17026)

苹果音乐网络应用[于](https://www.theverge.com/2019/9/5/20851576/apple-music-web-player-interface-beta-browser-streaming)在【beta.music.apple.com】的[面向用户推出公测。](https://beta.music.apple.com)[通过](https://www.reddit.com/r/webdev/comments/d0hdeo/the_new_official_apple_music_app_for_the_web_is/?utm_source=ifttt) [几篇](https://www.reddit.com/r/emberjs/comments/d06pcn/new_apple_music_web_app_built_in_ember/) [报道](https://www.linkedin.com/feed/update/urn:li:activity:6575525024327884800/)，[证实](https://www.linkedin.com/feed/update/urn:li:activity:6575756074194993152/)新的 Apple Music 网站是一个 Ember 应用。此外，新的[tv.apple.com](https://tv.apple.com)登陆和内容页面也是用 Ember 构建的[！](https://twitter.com/mehulkar/status/1171579484115353605)

[分享你的想法](https://discuss.emberjs.com/t/apple-music-beta-is-an-ember-app/17026)获得更多引人注目的 Ember 应用示例！

* * *

## [API 文档更新📝](https://github.com/emberjs/ember.js/pulls?utf8=%E2%9C%93&q=is%3Apr+is%3Aclosed+%5BDOC%5D)

感谢 [Ricardo Mendes (@locks)](https://github.com/locks) 和团队在[八进制化 API 文档](https://github.com/emberjs/ember.js/pulls?utf8=%E2%9C%93&q=is%3Apr+is%3Aclosed+%5BDOC%5D)！这些文档升级是[任务发布](https://github.com/emberjs/ember.js/issues/18250)的一部分，以更新 API 文档来显示辛烷和原生类。 [Jen Weber (@jenweber)](https://github.com/jenweber) 提供了**详细的分步说明**、YUIDoc 风格指南和一些关于[任务问题](https://github.com/emberjs/ember.js/issues/18250)的示例，鼓励有兴趣的贡献者在 [#dev-ember-learning](https://discordapp.com/channels/480462759797063690/480777444203429888) 上发表关于 [Ember Discord](https://discordapp.com/invite/zT3asNS) 的任何问题。

另一个正在流行的 API 文档问题是改进 EmberArray 的文档。浏览 http://api.emberjs.com/ember/release/classes/EmberArray 的[，你会注意到有些条目只有最少的文档，没有代码样本。目标是检查每个条目，在缺失的地方添加代码示例，并有选择地改进文章。在](http://api.emberjs.com/ember/release/classes/EmberArray) [EmberArray 问题](https://github.com/emberjs/ember.js/issues/18228)中的帖子上发表评论以作贡献。

想了解更多参与开源社区的方法，请查看 Ember 求助应用程序！

* * *

## [是什么让向 Ember 的过渡变得值得:一个新社区成员的观点🐹](https://twitter.com/lenoraporter_/status/1170145563549061122)

在最近的一条[推文中](https://twitter.com/lenoraporter_/status/1170145563549061122)，高级产品设计师[莲娜·波特(@莲娜·波特 _)](https://twitter.com/lenoraporter_)
分享了一个[视频](https://www.instagram.com/tv/B2CCM60AOZG/?igshid=12ivaiobj1y8h)，向那些从其他技术栈过渡的人推销关于 Ember 的**最有趣的学习**。

Lenora 之前在 React 有过经验，她谈到了对那些刚开始使用 Ember 的人有用的学习资源，以及是什么让这种转变值得。除了配置和集成工具上的约定之外，她还强调了一个支持性的社区如何让学习 Ember 变得非常简单。

请务必查看完整视频并与您的 JavaScript 朋友分享！

* * *

## [Ember Sinon QUnit 4.0 发布🎉](https://twitter.com/JordanHawker/status/1170107247118053376)

[乔丹霍克(@elwayman02)](https://github.com/elwayman02) 和[史蒂夫卡尔弗特(@scalvert)](https://github.com/scalvert) 联手为我们带来最好的 [Sinon.js](https://sinonjs.org/) 体验。💞

感谢他们的工作， [ember-sinon-qunit v4](https://github.com/elwayman02/ember-sinon-qunit) 允许我们进行一次性设置，在测试中直接使用`sinon`对象，并且永远不用担心在测试运行后清理模拟。要了解更多关于改变和新语法的动机，你可以看看[乔丹的博客文章](https://www.jordanhawker.com/p/187541610821)和[插件的自述文件](https://github.com/elwayman02/ember-sinon-qunit#usage)。

如果您一直在使用史蒂夫的 [ember-sinon-sandbox](https://github.com/scalvert/ember-sinon-sandbox) 或 [ember-sinon-sinoff](https://github.com/scalvert/ember-sinon-sinoff) ，请查看[自述文件](https://github.com/elwayman02/ember-sinon-qunit#migrating-to-ember-sinon-qunit)来选择您的迁移路径。

* * *

## [投稿人的角落👏](https://guides.emberjs.com/release/contributing/repositories/)

本周我们要感谢 [@stefanpenner](https://github.com/stefanpenner) 、 [@happyvig](https://github.com/happyvig) 、 [@chancancode](https://github.com/chancancode) 、 [@rwjblue](https://github.com/rwjblue) 、 [@imkathir](https://github.com/imkathir) 、 [@bekzod](https://github.com/bekzod) 、 [@wycats](https://github.com/wycats) 、 [@Gaurav0](https://github.com/Gaurav0) 、 [@runspired](https://github.com/runspired) 、 [@igorT](https://github.com/igorT) 💖

* * *

[![Office Hours Tomster Mascot](img/08fd3a60c87ed95f49c57070fc4a7fcd.png "Readers' Questions")](https://res.cloudinary.com/practicaldev/image/fetch/s--JAeu_q9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/97o89vnw1d3ryeldr7zp.png)

## [有问题吗？向读者提问！🤓](https://docs.google.com/forms/d/e/1FAIpQLScqu7Lw_9cIkRtAiXKitgkAo4xX_pV1pdCfMJgIr6Py1V-9Og/viewform)

想知道一些关于余烬，余烬数据，微光，或余烬生态系统插件的事情，但不知道去哪里问？读者提问只为你！

**提交自己的**短甜【bit.ly/ask-ember-core】下[的**问题**。别担心，没有愚蠢的问题，我们都很感激-我保证！🤞](https://bit.ly/ask-ember-core)

* * *

## [#embertimes📰](https://blog.emberjs.com/tags/newsletter.html)

想为《余烬时报》写稿吗？对下周的问题有什么建议吗？加入我们的 [#support-ember-times](https://discordapp.com/channels/480462759797063690/485450546887786506) 关于 [Ember 社区不和](https://discordapp.com/invite/zT3asNS)或者在 Twitter 上 ping 我们 [@embertimes](https://twitter.com/embertimes) 。

通过订阅我们的[电子邮件简讯](https://the-emberjs-times.ongoodbits.com/)，了解本周在恩伯兰发生的事情！你也可以在[余烬博客](https://emberjs.com/blog/tags/newsletter.html)上找到我们的帖子。

* * *

那是另一个包裹！✨

要善良，

吴宇森、李艾萨克、杰西卡·乔丹、林美儿和学习小组