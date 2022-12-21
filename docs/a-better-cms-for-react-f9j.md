# 一个更好的反应 CMS

> 原文：<https://dev.to/0xsven/a-better-cms-for-react-f9j>

<small><small><small>照片由哈尔·盖特伍德在 Unsplash 上</small></small></small>

你是 react 开发者吗？那么这个时代一定很精彩。[盖茨比](https://www.gatsbyjs.org/)、 [nextjs](//nextjs.org) 、 [netlify](https://www.netlify.com/) 、 [JAM stack](https://jamstack.org/) ，应有尽有。您可以立即设置一个复杂的 web 项目。

现代工具很神奇。但是，我想在这篇文章中谈论的是，现代 CMS 不是。使用今天的 CMS 解决方案让我感到沮丧。花在与 CMS 集成和管理内容及其结构上的时间感觉不太对。

我想了很多关于我的经历，并想出了一个新的方法来解决我的挫折。React 项目的更好的 CMS。让我进一步解释一下。

## 更好的开发者体验

有很多小的挫折点。对我来说最大的问题是内容结构。大多数 CMS 用某种类型来定义他们的内容结构。每个 CMS 都有那些:Wordpress 有[帖子类型](https://wordpress.org/support/article/post-types/)，CraftCMS 有[版块](https://docs.craftcms.com/v3/sections-and-entries.html#sections)，Contentful 有[内容类型](https://www.contentful.com/developers/docs/concepts/data-model/)。它们指的都是同一个:数据库中的一个表。这对于博客帖子或电子商务产品等结构化数据非常有用。

对于有很多独特元素的网站来说，这种方法并不奏效。很多时候，你的网站由只存在一次的东西组成。例如:一个关于一个英雄组件的网页，一个背景图片和一个 CTA 按钮。很难将网站标准化为数据库表格。

**解决方案:**
从代码中导出内容结构。猜猜什么最了解你的网站结构？背后的代码！

<figure>

[https://www.youtube.com/embed/ByfwjPee-Tk](https://www.youtube.com/embed/ByfwjPee-Tk)

<figcaption>Video showcasing developer experience.</figcaption>

</figure>

这也解决了我遇到的许多相关痛点:

*   无需通过 web-UI 手动定义内容结构
*   测试不同的内容值要容易得多
*   您可能已经在用 Git 对代码进行版本控制了。所以你的内容结构也是版本化的。
*   无需将内容结构迁移到其他环境，只需部署代码即可

## 更好的编辑体验

CMS 用户界面让编辑们很为难。我们开发人员有时看不起编辑，因为他们很难理解 CMS 是如何工作的。但是请想一想:在一个嵌套的树结构中隐藏着许多输入字段。编辑必须将这些与他们头脑中的网站相匹配。而不知道代码或内容结构。

无头 CMS 变得流行是因为它们让开发者选择他们想要的技术。结果，网站离 CMS 越来越远。编辑是吃亏的一方。

**解决方案:**
合并 CMS-UI 和网站。编辑应该有编辑网站的感觉。每一个变化都会立即反映在网站上。我们用 React 这个强大的前端库建立网站。为什么不用它来驱动 CMS 呢？

<figure>

[https://www.youtube.com/embed/_5Z8KegSd9k](https://www.youtube.com/embed/_5Z8KegSd9k)

<figcaption>Video showcasing editor experience.</figcaption>

</figure>

## 接下来是什么？

我现在只有一个使用本地存储的原型(如上面的视频所示)。下一步将是发布一个 NPM 模块，并开发一个简单的数据同步服务，所以我们可以在现实世界中尝试这一点。

你受够了 CMS 的景观吗？你认为上面描述的方法怎么样？我非常感谢每一个意见和每一个回复。感谢阅读。