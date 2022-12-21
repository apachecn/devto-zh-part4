# 使用 Angular 和 Akita 管理您的州导航数据轻而易举

> 原文：<https://dev.to/theblushingcrow/managing-your-state-navigation-data-is-a-breeze-using-angular-and-akita-1kg8>

Akita ng-router-store 是一个提供给 Akita 用户的简洁包，它能够将 Angular 路由器状态绑定到 Akita 商店。这种方法的好处是:

*   路由器状态存储在一个名为 router 的 Akita 存储中，它是 URL、状态数据参数、查询参数等信息的唯一来源。
*   我们的组件不需要熟悉数据源。RouterQuery 可以注入到任何一个查询中，以创建一个封装的派生选择器。
*   该库公开了 Akita 风格的查询，使您不必处理所有必需的样板代码。
*   我们可以在 Redux devtools 中检查当前路由器状态，并执行“时间旅行”。首先，安装软件包并将其以及 Akita 的 devtools 模块添加到 app 模块: