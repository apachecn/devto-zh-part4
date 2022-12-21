# JS&Friends Conf:测试他们的 JavaScript-Rob Tarr

> 原文：<https://dev.to/aromig/js-friends-conf-test-them-javascripts-rob-tarr-4fo1>

## 测试他们的 JavaScript-[罗布塔尔](https://twitter.com/robtarr)

[![Rob Tarr](img/ff5340136ffd99ce218676e1ebbd53ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UFgrW_o7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a1dvuimw28113ggzbazh.jpg)

来自 [Sparkbox](https://seesparkbox.com) 的 Rob 用几个问题开始了他关于测试的讨论:

*   测试对你的团队意味着什么？
*   你是怎么测试的？
*   你多久测试一次？

部分答案:

*   想法一致
*   开发时在浏览器中手动操作(TDD ),使用测试库
*   很少，在整个 CI 过程中，在推送到存储库之前

他继续解释为什么我们要测试我们的代码。它确实给了我们一些想法和一致性，也给了我们对代码和自己的信心，并因此设计出更好的软件。但是我们从哪里开始呢——尤其是当 web 开发已经有了这么长时间不测试的文化之后？不管是什么原因，这很难，没有时间或者只是事后的想法。

Rob 列出了几个测试框架:Mocha、艾娃、Jest 和 Jasmine 尽管在这短暂的时间里，他打算把注意力集中在摩卡咖啡上。他提到了不同的*跑步者*用于启动测试:咕噜声、大口声、Karma、Make、npm。

建立测试内容的界限是非常重要的一点，这样就不会浪费时间去测试那些(可能)已经测试过的东西。他称之为修剪自家院子的概念。不要测试库和 API。只需测试你的代码。

我相信应该有更多的演讲，可能还有如何开始编写测试的例子——我从哪里开始呢？但是只有 50 分钟的时间，可能会缩短。我意识到我在自动化测试方面的知识差距，我肯定会在这方面加强自我教育。

[←回主 JS &好友文章](https://dev.to/aromig/my-first-dev-conference-javascript-friends-fh7)