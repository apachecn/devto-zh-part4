# 试用并发布 next . js 9.0+TypeScript+redux-observable starter

> 原文：<https://dev.to/kunihikot/tried-published-next-js-9-0-typescript-redux-observable-starter-2p33>

Next.js 9.0 已经发布。我用我常用的开发库(redux-observable，styled components，等等)进行了尝试...)，我发现了一些问题，所以我写下来了。

## redux-可观察

在 Next.js 中使用 redux-observable，我们必须将一些代码放到`/pages/_app.ts`中，与纯 react 开发相比，风格略有不同。有一些图书馆可以帮助它

*   [下一个重复包装器](https://github.com/kirill-konshin/next-redux-wrapper)
*   下一个还原传奇但是没有下一个还原可观察的东西。所以，最终我写了下面的代码。[https://github . com/kuni hiko-t/nextjs 9-ts-redux-observable-starter/blob/master/pages/_ app . tsx](https://github.com/kunihiko-t/nextjs9-ts-redux-observable-starter/blob/master/pages/_app.tsx)# # CSS 导入我试过用[语义 UI React](https://react.semantic-ui.com/) 。但是当我写下一行时，我得到了一个错误。

```
import 'semantic-ui-css/semantic.min.css' 
```

在 Next.js 的世界里，我们要创建 next.config.js，而不是 webpack.config.js。
所以我在上面写了 css-loader 设置。但是效果不好。
我谷歌了一下&找到了关于`@zeit/next-css`的信息。
但是 next-css 显示类似`Invalid Option`的错误，所以我谷歌了更多，我找到了[的问题](https://github.com/zeit/next-plugins/issues/392)。这只是权宜之计，但效果很好。
我的 next.config.js 在下面。
[https://github . com/kuni hiko-t/next js 9-ts-redux-observable-starter/blob/master/next . config . js](https://github.com/kunihiko-t/nextjs9-ts-redux-observable-starter/blob/master/next.config.js)

## 样式组件

浏览器控制台上有一些错误。
我又谷歌了一下，一些文章说使用`.babelrc`。
所以我添加了`.babelrc`，它解决了这个问题。

```
{
"presets": ["next/babel"],
"plugins": ["styled-components"]
} 
```

## 发布首发

目前没有错误。我觉得使用 Next.js 和纯粹的 react 开发有点不同，这让我很困惑。
于是我发表了这篇文章&[Next.js 9.0+TypeScript+redux-observable starter](https://github.com/kunihiko-t/nextjs9-ts-redux-observable-starter)对于我，以及正在尝试 next . js 却在错误中挣扎的人。
但是这个仓库仍然是 WIP。

*   [ ]使用中间件或其他东西进行身份验证
*   [ ]为`import { foo } from '~/foo'`设置别名
*   [ ]从 epic 中删除“任何”类型