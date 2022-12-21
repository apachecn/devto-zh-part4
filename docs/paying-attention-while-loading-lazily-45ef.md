# 懒散地加载时要注意

> 原文：<https://dev.to/jeffposnick/paying-attention-while-loading-lazily-45ef>

### 这些都是什么？

这是一个交互式项目，探索当<mark>加载最佳实践</mark>(散列资产 URL、延迟加载、服务人员)遇到<mark>单页面应用使用模式</mark>(长期标签、通过历史 API 的“假”导航)遇到<mark>常见部署场景</mark>(不保留旧 URL 的重新部署)时会发生什么。

如果你觉得特别元，你可以观看这种材料的视频:

[https://www.youtube.com/embed/9Ifnvm9nWs8](https://www.youtube.com/embed/9Ifnvm9nWs8)

### 它是如何工作的？

1.  访问以下加载场景之一，并保持页面打开。
2.  在故障编辑器中，对组件进行更改(如 [`About.js`](https://glitch.com/edit/#!/pawll?path=common/About.js) )
3.  使用[故障控制台](https://glitch.com/help/console/)，运行`webpack --config=path/to/webpack.config.js`来重建你已经打开的场景。
4.  你刚刚“重新部署！”浏览打开的场景页面，查看哪些有效(哪些无效)。

(更新 web 应用的 JavaScript [不会触发](https://glitch.com/edit/#!/pawll?path=watch.json)自动重建，尽管更新 [`server.js`](https://glitch.com/edit/#!/pawll?path=server.js) 会。)

### 正在加载要尝试的场景

*   [无哈希，无延迟加载](https://pawll.glitch.me/1-no-hash-no-lazy/build/)
*   [无哈希，带延迟加载](https://pawll.glitch.me/2-no-hash-lazy/build/)
*   [散列资产 URL，延迟加载](https://pawll.glitch.me/3-hash-lazy/build/)
*   [散列的资产 URL，带有延迟加载和错误边界](https://pawll.glitch.me/4-hash-lazy-errorboundary/build/)
*   [散列资产 URL，带有延迟加载、错误边界和服务工作者预缓存](https://pawll.glitch.me/5-hash-lazy-errorboundary-sw/build/)

### 了解更多信息

#### 加载 JavaScript

*   [通过代码分割减少 JavaScript 负载](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/code-splitting/)
*   [React 的代码拼接文档](https://reactjs.org/docs/code-splitting.html)
*   [React 的错误边界文件](https://reactjs.org/docs/error-boundaries.html)

#### 服务人员

*   [服务人员:介绍](https://developers.google.com/web/fundamentals/primers/service-workers/)
*   [工具箱](https://developers.google.com/web/tools/workbox/)
*   [工具箱的预缓存](https://developers.google.com/web/tools/workbox/modules/workbox-precaching)
*   [激发这个项目的`create-react-app`讨论](https://github.com/facebook/create-react-app/issues/3613#issuecomment-353467430)

#### HTTP 服务

*   [缓存最佳实践&最大年龄陷阱](https://jakearchibald.com/2016/caching-best-practices/)
*   [单页应用如何工作](https://blog.pshrmn.com/entry/how-single-page-applications-work/)