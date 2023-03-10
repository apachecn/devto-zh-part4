# 学习应该是有趣的:ReactJS + Twitch API 教程

> 原文：<https://dev.to/renaissanceengineer/learning-should-be-fun-reactjs-twitch-api-tutorial-4i6p>

## 这个世界需要多少 todo apps 和计算器？

学习编码很难，这使得有时很难保持动力。大多数初学者教程几乎总是相同的重复项目，这不仅令人厌烦，而且不会帮助你找到工作

我认为最好的学习方法是把你喜欢的东西混合在一起，创造出新的东西。一个例子是通过使用 Twitch API 将编程与游戏结合起来

[https://www.youtube.com/embed/VTY6ZzDTV3A](https://www.youtube.com/embed/VTY6ZzDTV3A)

我做这个教程是考虑到初学者的。一旦你完成了本教程，你将有许多选项来扩展基础项目，为你自己的投资组合做出一些独特的东西。您可以添加的一些功能示例:

*   使用图表库创建一个实时的统计数据仪表板，并每分钟调用 API 来更新视图计数
*   创建一个个人资料路线并嵌入视频流，而不是直接链接到 twitch[https://dev.twitch.tv/docs/embed/](https://dev.twitch.tv/docs/embed/)
*   改变用于显示 Twitch API 数据的卡片的样式
*   添加一些错误处理来处理 API 限制或中断之类的事情
*   添加一个加载组件来包装我们的列表，以显示一个加载图标，直到 API 调用返回
*   打破一些重复的逻辑，比如替换 URL，为重复的 UI 逻辑创建一个列表组件和卡片组件

本教程假设您已经掌握了 ReactJS 的一些基本知识，所以请确保您至少已经阅读了主要的 [Reactjs 入门指南](https://reactjs.org/docs/hello-world.html)，并且理解了 React 的基本概念，如 JSX、状态管理以及如何使用组件。

完成后，请随意留下您项目的链接，这样其他人就可以看到您的成果。