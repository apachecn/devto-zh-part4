# 这个。JavaScript-框架和库的状态-React 更新

> 原文：<https://dev.to/thisdotmedia/this-javascript-state-of-frameworks-and-libraries-react-update-4e7b>

由这个主持。JavaScript，这是一个在线活动，开发者可以从中了解 JavaScript、框架和库的最新消息，涵盖了框架领域的所有突发新闻。

[2 月 19 日](https://www.youtube.com/watch?v=DFF9eOlTWzY)，State of Frameworks 的演讲者，包括许多来自开发界的最优秀和最聪明的人，向我们介绍了有关所有框架的最新情况。

React 路由器的创造者和所有者 Michael Jackson 分享了一些关于 React 即将推出的[的更新。](https://reactjs.org/)

[![Michael Jackson](img/57d8405c416410dd3d6547082ffd47e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rxaI2i-S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2APPO83n2etmhQ9PPw)

## 迈克尔杰克逊——创作者，React 路由器&所有者 React 培训——[@迈克尔杰克逊](https://dev.to/mjackson)

## 新增功能

### 代码分割

React 的 16.6 版本现在具有代码拆分功能。代码分割是基于这样一种思想，即不是向您的用户交付一个大的包，您只是向他们提供主包，并可以根据需要向他们提供其他更小的包。

React.lazy 和 React .悬念是管理代码分割的机制。[查看 React 文档](https://reactjs.org/docs/getting-started.html#versioned-documentation)了解关于该特性的更多详细信息。

### 挂钩

钩子允许你“挂钩”到 React 16.8 中提供的底层 React 生命周期和其他特性。

目前有 7-8 个钩子可用，每个钩子都有不同的功能——为开发人员开辟了许多新的可能性。例如，“Refs”处理渲染调用之间的持久值，而“Effect”可能会产生副作用或“不纯粹”的东西，如计时器和命令性 DOM 突变。

React 钩子是稳定的，在 16.8 中发布。如果你去[Reacttraining.com](https://reacttraining.com/)，你可以注册参加我们即将举办的研讨会，在那里你可以学到更多关于挂钩的知识以及如何使用它们。

## 即将推出的功能

React 将在明年推出许多新功能。

一个是 React 并发模式，这是指 React 有能力优先处理某些类型的工作。例如，像滚动或按键这样的用户交互可以优先于其他交互，比如在主线程上呈现一些视图。并发模式应该会在 2019 年晚些时候登陆。

并发模式也将增强暂停模式，带来更好的性能可能性。如果你现在正在加载一个包，你会得到一个微调和超时。然而，悬念模式会给你等待工作发生的能力。

2019 年，React 还将推出对悬念模式内数据提取的支持。通过数据获取，您可以在呈现树之前等待一些异步 IO 发生。

最后，React 团队用他们用于异步实现的 API 实现了一个简单的缓存模型。React 缓存模块也将于 2019 年发布。

## ReactJS.org 翻译

除了 React Core，ReactJS.org 也在进行一些令人兴奋的更新。

《ReactJS.org》被翻译成世界各地的多种语言。目前，已有超过 16–17 个 repos 版本，语言包括法语、西班牙语、葡萄牙语、亚美尼亚语、荷兰语、阿拉伯语和许多其他语言。这将允许更多的贡献者参与进来。

如果你想帮助正在进行的翻译项目或任何其他 React 计划，请前往[github.com/reactjs.](https://github.com/reactjs)