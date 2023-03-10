# Gridsome 简介

> 原文：<https://dev.to/jaeyholic/introduction-to-gridsome-440b>

> Gridsome 是一个 Vue.js 驱动的现代站点生成器，用于为任何无头 CMS、API 或 Markdown-file 构建尽可能最快的网站。Gridsome 使开发人员无需成为性能专家就能轻松有趣地创建快速、漂亮的网站。

[![gridsome](img/1730b1051a57ade285b084a5459a15dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--am1AY8Ln--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dExQAI0.png)

### 为什么是 Gridsome？

*   **热重装本地开发** -实时查看代码变化。
*   数据源插件 -用于任何流行的无头 CMS、API 或 Markdown 文件。
*   **基于文件的页面路由** -使用文件快速创建和管理路由。
*   **集中式数据管理** -将数据拉入本地统一的 GraphQL 数据层。
*   **vue . js for frontend**——一个轻量级的、可接近的前端框架。
*   **自动优化的代码** -获得开箱即用的代码分割和资产优化。
*   **静态文件生成** -安全部署到任何 CDN 或静态 web 主机。

[![vuejs](img/f81006aa020d583b43778c8549e88e3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pXxQ2-EB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ee4RxwN.png)

[了解更多关于 Gridsome 如何工作的信息](https://gridsome.org/docs/how-it-works)

### 1。安装 Gridsome CLI 工具

使用纱线:
`yarn global add @gridsome/cli`

使用 npm:
`npm install --global @gridsome/cli`

### 2。创建 Gridsome 项目

1.  `gridsome create my-gridsome-site`创建新项目
2.  `cd my-gridsome-site`打开文件夹
3.  `gridsome develop`在`http://localhost:8080`启动本地开发服务器
4.  快乐编码🎉🙌

### 3。后续步骤

1.  在`/pages`目录中创建`.vue`组件来创建页面路由。
2.  使用`gridsome build`在`/dist`文件夹中生成静态文件

[![code](img/8fe9e66e22101e2c4cc734e67a95e0e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7-9ZP0iI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6wM4lNE.png)

*   [工作原理](https://gridsome.org/docs/how-it-works)
*   [页面如何工作](https://gridsome.org/docs/pages)
*   [如何部署](https://gridsome.org/docs/deployment)