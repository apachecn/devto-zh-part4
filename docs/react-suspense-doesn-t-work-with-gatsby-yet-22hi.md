# 做出反应。悬念对盖茨比不起作用

> 原文：<https://dev.to/dance2die/react-suspense-doesn-t-work-with-gatsby-yet-22hi>

*照片由[егоркамелев](https://unsplash.com/@ekamelev?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄于 [Unsplash](https://unsplash.com/search/photos/spider?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

*(选择这张照片是因为它是一个能引起悬念的可爱小动物😅)*

我使用 [React.lazy](https://reactjs.org/docs/code-splitting.html#reactlazy) 在 [Gatsby](https://www.gatsbyjs.org) 上动态加载组件，这需要使用 [React。悬疑](https://reactjs.org/docs/code-splitting.html#suspense)。

但是后来我在建网站的时候收到了下面的消息。

*事实上，我是在 Netlify 上部署它时发现的😅(然后跑去* `gatsby build` *当地)*

> WebpackError:固定冲突:Minified React 错误# 294；请访问[https://reactjs.org/docs/error-decoder.html?invariant=294](https://reactjs.org/docs/error-decoder.html?invariant=294)查看完整信息，或者使用非简化开发环境查看完整错误和其他有用警告。

<figure>[![](img/6d8b7f8103729ab782c286b4194c5bbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rUIC-JwG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.slightedgecoder.com/wp-content/uploads/2019/07/build-error-1.jpg%3Ffit%3D1024%252C396%26ssl%3D1) 

<figcaption>字体在本地机器上看起来太破了对了</figcaption>

</figure>

错误信息将您指向[https://reactjs.org/docs/error-decoder.html?invariant=294](https://reactjs.org/docs/error-decoder.html?invariant=294)，这表明

> **ReactDOMServer 尚不支持悬念。**

清晰的信息(没有讽刺的意思)。

Gatsby [在构建过程](https://github.com/gatsbyjs/gatsby/blob/57390e8da2d89c6abd0ad235c7389f39d072a36f/docs/blog/2018-11-07-gatsby-for-apps/index.md#gatsby-is-for-applications)中使用了 ReactDOMServer，因此出现了错误。

## 修复违规代码

下面是使用 React.lazy 导致问题的违规代码。