# 纱线工作区-按路线拆分 React 应用程序

> 原文：<https://dev.to/letitrock/yarn-workspaces-splitting-react-app-by-routes-55oo>

开发者们好！关于我们团队的想法，我需要你的建议/帮助。

我们有一个巨大的 React/Redux/Sagas 应用程序，它的重量很大，所以我们决定根据路线来划分它。但是突然一个想法出现在我们的脑海中:为什么不把那些“分割路线”代码转移到单独的纱线包中(用减速器、sagas 等)？我们认为将来会更容易管理。

将代码分割成多个可以用 Webpack 动态导入的块还需要我们实现额外的逻辑，以便在浏览器下载块后导入 reducers 和 sagas，但这是可行的。

你能分享一下你对此的看法吗？这样使用 yarn 工作空间真的有意义吗？

谢谢！