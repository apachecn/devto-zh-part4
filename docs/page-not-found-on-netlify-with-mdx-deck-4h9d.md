# 在带有 MDX-Deck 的 Netlify 上找不到页面

> 原文：<https://dev.to/dance2die/page-not-found-on-netlify-with-mdx-deck-4h9d>

*照片由[黑脸田鸡·佐野](https://unsplash.com/@s_sagano?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/deck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

在将 [MDX-Deck](https://github.com/jxnblk/mdx-deck) 演示文稿部署到 Netlify 后，您可能会遇到以下错误。

*请参考我的朋友，[戴夫·福莱特](https://twitter.com/davefollett)关于* [*如何部署到 Netlify*](https://dev.to/davefollett/learning-mdx-deck-deploy-to-netlify-5e78) *的帖子。*

*如果您在使用 React 路由器时遇到问题，请使用 React 路由器* *查看 Netlify 上未找到的* [*页面。*](https://dev.to/dance2die/page-not-found-on-netlify-with-react-router-58mc)

> **找不到页面**
> 
> 看起来你点击了一个断开的链接，或者输入了一个本网站上不存在的 URL。

[![Page Not Found](img/ff66c1d8942b1413318a7d836f3e46ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M0L8T4xQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2019/07/page-not-found.jpg%3Fw%3D1170%26ssl%3D1)

## 错误是怎么发生的？

MDX-Deck(版本<= 2 is based on [create-react-app](https://facebook.github.io/create-react-app/) 而 v3 基于 [Gatsby](https://www.gatsbyjs.org/) )路由在客户端处理，而 Netlify(服务器端)不知道如何处理路由。

## 怎么才能修好？

Netlify 提供了一个特殊的文件， [_redirects](https://www.netlify.com/docs/redirects/) ，您可以将它添加到您的代码库中，并让 Netlify 处理如何处理客户端没有处理的 URL。

但问题是，当您构建 MDX-deck 内容时，顶层的`_redirect`文件不会被复制到`public/`分发文件夹中。

因此，您可以指定与在 _redirects 文件中相同的重定向规则。

*Netlify TOML 文件包含一个指导 Netlify 如何构建&部署你的站点*