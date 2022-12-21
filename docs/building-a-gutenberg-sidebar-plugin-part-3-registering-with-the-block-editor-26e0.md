# 构建 Gutenberg 侧边栏插件第 3 部分:向块编辑器注册。

> 原文：<https://dev.to/vanaf1979/building-a-gutenberg-sidebar-plugin-part-3-registering-with-the-block-editor-26e0>

[最初发布于 2019 年 9 月 15 日我的网站](https://since1979.dev/building-a-gutenberg-sidebar-plugin-part-3-registering-with-the-block-editor/)

### 简介

在[上一篇文章](https://since1979.dev/building-a-gutenberg-sidebar-plugin-part-2-adding-structure-and-asset-bundling/)中，我们用 Laravel Mix 完成了资产捆绑的设置。下一步是开始添加一些 Javascript 代码，用古腾堡块编辑器注册我们的插件。

因为 Gutenberg 是使用 ReactJs 构建的，所以创建一个插件也需要我们使用这个框架来编码。如果你不熟悉 React，我强烈推荐你参加 Scrimba 上[鲍勃·齐罗尔](https://twitter.com/bobziroll)的[免费学习 React](https://scrimba.com/g/glearnreact)课程。但是如果你对普通 Javascript 足够熟练，你应该能够理解。

现在让我们进入有趣的部分🙂

### 导入注册插件

为了注册我们的插件，Gutenberg 提供了一个很好的助手函数，叫做，你猜对了， *registerPlugin* ！但是在我们使用它之前，我们必须进口它。

打开 *src/js/metatgs.js* 文件，删除我们放在那里用于测试的任何代码，并添加下面的代码片段。