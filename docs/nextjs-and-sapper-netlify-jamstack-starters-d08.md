# Nextjs 和 Sapper Netlify Jamstack 启动器

> 原文：<https://dev.to/mrispoli24/nextjs-and-sapper-netlify-jamstack-starters-d08>

几周前，我偶然看到了一些 Gatsby 和 Netlify 教程，并对 jamstack 的发展感到好奇。我不打算抨击 jamstack 是什么，但我对这场无服务器革命有所怀疑。尽管如此，我还是决定运行一下这个设置，并对它感到惊喜，不是因为它没有服务器，而是因为它提供了 Netlify。身份验证、lambdas 和联系表单——都有慷慨的免费限制？

这似乎是一个难以置信的平台来建立早期原型和测试不同的 SaaS 想法。对于客户端工作，它似乎大大降低了 DDoS 攻击和服务器在半夜停机的风险。唯一的问题是我害怕完全依赖盖茨比。我发现 Gatsby 是静态站点生成的一个极好的平台，并且已经用它建立了一些小型组合站点和博客。然而，从长远来看，你只能停留在一个完全静态的框架上。现在我已经开发了相当多的软件，我知道一个人不能完全预测他们在项目开始时会遇到的障碍。今天看似完美的 jamstack 可能会变成一个需要良好服务器环境的项目。此时你只有一个选择，迁移。

进入 next.js，这是我几乎所有事情的首选平台。一个易于使用、健壮的基于 React 的框架，只是有点固执己见。它有一个很棒的社区，还有很棒的测试和类型脚本支持，以及所有你可能希望在项目变得太大之前就设置好的东西。它还可以像盖茨比一样静态导出页面。最大的不同是，如果你想使用服务器环境，并转移到像 Heroku 或 Digital Ocean 这样的平台，你可以。服务器端渲染对于您的认证用户来说也是一个不错的选择。

在我的研究中，我还在未定义的播客上发现了一个叫做 Svelte 的新框架。我对它基于编译器的构建健壮前端的方法非常感兴趣，所以我做了更深入的研究。我发现了一个很棒的平台，叫做 Sapper，它是由同一个团队建立的，这个团队似乎吸取了 Next.js 的精华，为 Svelte 建立了一个类似的环境。我现在不想讨论每个框架的优缺点，重点是它值得一试。就像 Next.js 一样，它的特点是静态导出和服务器端呈现，如果您想要启用服务器，可以使用这些功能。

最后，我想为每一个都提供一个样板，这样我就可以很容易地创建原型、客户站点和 JAMstack 应用程序。如果我发现无服务器模式不适合我，我还想选择退出而不丢失我所有的工作。

所以我给你两个回复:

1.  [https://github.com/mrispoli24/next-netlify-jamstack-starter](https://github.com/mrispoli24/next-netlify-jamstack-starter)
2.  [https://github . com/mrispoli 24/sapper-netlify-jam stack-starter](https://github.com/mrispoli24/sapper-netlify-jamstack-starter)

我的想法是依靠我的付费客户工作的下一步，因为它是战斗测试和生产使用批准。我将在我的个人项目中使用 sapper，我愿意花一些时间在一个早期的框架中对抗早期的错误。我也觉得前端苗条的做法值得学习和好好学习。

由于我在谷歌搜索中找不到更多类似的框架，我希望这能帮助一些人更快开始。

我还将添加 netlify 认证，并希望与条纹支付处理，所以你可以有一个“蒂姆费里斯”式的注册实验解决方案，尝试 x 天，按月支付。有点像在已经建有厨房的大楼里开餐馆。