# 构建 Gutenberg 侧边栏插件第 4 部分:添加表单组件。

> 原文：<https://dev.to/vanaf1979/building-a-gutenberg-sidebar-plugin-part-4-adding-form-components-1kil>

[最初于 2019 年 9 月 22 日发布在我的网站上](https://since1979.dev/building-a-gutenberg-sidebar-plugin-part-4-adding-form-components/)

### 简介

在[上一篇文章](https://since1979.dev/building-a-gutenberg-sidebar-plugin-part-4-adding-form-components/#)中，我们使用 ReactJs 和 *registerSidebar* 辅助函数为 Gutenberg 编辑器添加了一个侧边栏。在本文中，我们将为浏览器标题、元描述和 Robots 标签添加一些表单域组件，这样我们就可以实际编辑我们的 Seo 元数据。

### 添加浏览器标题。

首先是浏览器标题字段。下面可以看到编辑好的 *sidebar.js* 文件。