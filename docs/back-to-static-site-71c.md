# 返回静态站点

> 原文：<https://dev.to/onmyway133/back-to-static-site-71c>

我写 [Hello world 已经有一段时间了，再一次写](https://github.com/onmyway133/blog/issues/1)，GitHub 发布的便利性确实激励我写更多。

同时，我也在 https://medium.com/@onmyway133 的[和 https://dev.to/onmyway133 的](https://medium.com/@onmyway133)和[上写文章，并获得了一些关注。](https://dev.to/onmyway133)

然后我又开始用 GitHub 页面，有了 Jekyll 和 remote 主题，效果很棒。但之后我需要手动将 GitHub 问题链接到我的页面上，这只是体力劳动。

最好的组合是有一个由 GitHub issue 支持的 GitHub 页面。在比较了不同的静态站点生成器之后，我实际上尝试了所有的生成器，我选择了 Hexo，因为我只是喜欢 Javascript

*   [如何使用 Hexo 部署静态站点](https://github.com/onmyway133/blog/issues/392)
*   [如何使用 GraphQL 获得所有 GitHub 问题](https://github.com/onmyway133/blog/issues/393)

我现在使用简单的[仙人掌](https://github.com/probberechts/hexo-theme-cactus)主题，由 https://www.npmjs.com/package/hexo-generator-search 的[提供本地搜索能力。](https://www.npmjs.com/package/hexo-generator-search)

然后，我编写了一个 node.js 脚本，将我的 GitHub 问题反映到我的页面上，使用正确的标签和更新的日期。

如果你有机会访问我的新页面[https://onmyway133.github.io/](https://onmyway133.github.io/)，我向你问好👋