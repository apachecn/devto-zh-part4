# next . js 9.0+typescript+redux-可观察を試してstarterとして公開した

> 原文：<https://dev.to/kunihikot/next-js-9-0-typescript-redux-observable-starter-3dob>

因为出现了 Next.js 9.0，所以试了一下，在很多地方都着迷了，所以作为笔记留下。

## 在导入 redux-observable 的阶段陷入

在 Next.js 上使用 redux 和 redux-observable 等的时候好像会在`pages/_app.ts`上写处理，和往常的情况不一样，陷入了困境。
好像有[next-redux-wrapper](https://github.com/kirill-konshin/next-redux-wrapper) 之类的，但最终没有使用，而是用[这样的感觉](https://github.com/kunihiko-t/nextjs9-ts-redux-observable-starter/blob/master/pages/_app.tsx)写的。

## 用 CSS 的 import 沉迷

[在导入 Semantic UI React](https://react.semantic-ui.com/) 时

```
import 'semantic-ui-css/semantic.min.css' 
```

在这种情况下，想要导入 CSS 时，出现了错误，陷入了困境。
在 Next.js 上，好像把在 webpack.config.js 上写的设定写在 next.config.js 上，所以试着写了一下，但是一下子就动不了。
然后发现了使用`@zeit/next-css`就可以工作的信息，所以试了一下，被称为 Invalid Option 等，不动再搜索，发现了可能有[关系的 issue](https://github.com/zeit/next-plugins/issues/392) 。
虽然只是 workaround，但总之先动起来了。
next.config.js 变成了[这样的](https://github.com/kunihiko-t/nextjs9-ts-redux-observable-starter/blob/master/next.config.js)。

## 自称 Componentsでハマる

在浏览器中打开画面时，console 出现了错误，所以调查后发现在. babelrc 上

```
{  "presets":  ["next/babel"],  "plugins":  ["styled-components"]  } 
```

得到了可以追述这一点的信息，追述了之后就解决了。

## 因为感觉可以开始开发了，所以公开了

这样一来，总之错误也消失了，可以工作了，进入了可以开始开发的状态。
因为如果不使用 Next.js 的话就不会特别在意的事情而着迷，非常麻烦。
为了今后的自己，也为了同样想碰 Next.js 但是没有像往常一样麻烦地扔出去的人，用`yarn dev`留下了马上就能开始开发的东西。
[https://github.com/kuni hiko-t/next js9-ts-redux-observable-starter](https://github.com/kunihiko-t/nextjs9-ts-redux-observable-starter)
Alias 的设定还很好
Next.js 9 看起来很不错，希望能快点赶上 Nuxt.js 的人气