# 牛逼的 NestJS 样板

> 原文：<https://dev.to/narekhakobyan/awesome-nestjs-boilerplate-4o3o>

## 🤓关于我。

大家好，我的名字是纳瑞克，这是我在 dev.to 的第一篇文章，如果你喜欢，你可以点击❤️

## 📝简要概述

作为一名开发人员，当我们开始一个新项目时，我们的首要工作是找到具有良好应用架构、可维护代码和最新 Vue 版本的样板文件。但是我们有一个缺点，那就是样板文件，试图找到并发现大部分都是为那里的使用而写的。它的架构，代码风格，linter，和已经写好的帮助函数都是为项目配置的，你不能确定样板文件是为你的项目提供的。有许多解决方案，但有效的解决方案是通过添加我们需要的功能来创建或编辑现有的样板文件，并删除所有不再使用的“垃圾”代码(请确保将有大量代码始终未使用)。

## 🤩介绍

我想向你介绍[棒极了的 nest 样板文件](https://github.com/NarHakobyan/awesome-nest-boilerplate)，我们基于我们遇到的共同需求和问题创建了它。样板构建在 [NestJS 框架](https://nestjs.com/)之上，如果你不熟悉 NestJS，我建议尽快学习，因为它是最好的和进步的 Node.js 框架。

### Tslist

已经安装并配置了 tslint with appellister，所以您不需要担心代码风格。此外，漂亮支持最好的自动校正功能，所以你可以非常快速地修复所有的棉绒错误按下💡图标。此外，默认情况下，我们启用了预提交挂钩，因此无法提交不符合您的代码风格的代码。您可以通过编辑 tslint.json 文件来配置 linter 规则。

### Swagger API 文档

我们是集成的 swagger API 文档工具，它将从您的代码中生成 API 文档。所以不需要互相分享邮差收藏。

### 基于角色的访问控制

下一个常见的要求是 RBAC。所以我们把它整合到我们的项目中。它非常容易使用，只需在您的动作中添加`@Role()` decorator 即可。

[![](img/a1d5229c52edc77a84209d4602919e1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A9hu-wxT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/njkrq1ldg99nerydfjby.png)

### 证明

90%的新 SPA 应用程序都基于 JWT 认证，因此我们将其添加到样板文件中。只需将`@UseGuards(AuthGuard)`和`@UseInterceptors(AuthUserInterceptor)`装饰器添加到您的动作或控制器中，您就拥有了受保护的路径

### 数据传输对象

这是一个小部分，但最有趣的。你可以用 DTO 建筑。用于向客户端发送或接收数据。此外，我们为实体添加了额外的`toDto`,为实体数组添加了`toDto`,以便将实体快速转换为 dto

[![](img/bb373b360ef10f0bf5287c831773face.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kGdmGrGz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1bsnmbxyvwz238r4csf2.png)

*看看 **[github 资源库](https://github.com/NarHakobyan/awesome-nest-boilerplate)** 的样板文件，你会发现很多其他很酷的特性，你可以用在你的项目中。*