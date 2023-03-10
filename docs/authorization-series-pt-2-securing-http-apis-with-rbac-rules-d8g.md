# 授权系列—第 2 部分:用 RBAC 规则保护 HTTP APIs

> 原文：<https://dev.to/auth0/authorization-series-pt-2-securing-http-apis-with-rbac-rules-d8g>

编者按:这是关于授权的 3 篇系列文章中的第二篇技术文章。请继续关注本系列的最后一部分，重点是动态授权。

TL；DR:在本文中，您将了解如何利用 Auth0 的 RBAC(基于角色的访问控制)特性来处理 API 中的最终用户授权。本文将首先提出一个简单的场景，在这个场景中，您可以使用 RBAC 来保护 API 端点；然后，它将展示如何在 Auth0 仪表板中映射这个场景，以及如何在 Node.js 和 Express API 上实现它。尽管这里展示的示例使用了这种特定的技术组合(Node.js 和 Express)，但是您会发现该解决方案很容易实现，并且可以将其移植到您可能正在使用的任何其他平台上。

重要说明:本文利用了目前处于测试阶段的群组功能。要访问此功能，请按照授权组新测试计划公告上的说明进行操作。

[继续阅读📖](https://auth0.com/blog/authorization-series-pt-2-securing-http-apis-with-rbac-rules/?utm_source=dev&utm_medium=sc&utm_campaign=authorization_pt2)