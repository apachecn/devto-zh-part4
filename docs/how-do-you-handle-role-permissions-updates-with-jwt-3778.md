# 您如何处理 JWT 的角色/权限更新？

> 原文：<https://dev.to/sebastiandg7/how-do-you-handle-role-permissions-updates-with-jwt-3778>

通常，REST Api 后端中的 JWT 实现将用户角色和/或权限保存在 JWT 令牌声明中。在这种情况下，客户端利用这种声明来限制用户与某些应用程序功能的交互。

有些用户或服务器操作会更新用户的授权角色/权限。然而，**最后发出的 JWT 令牌还没有过期**，因此在其声明中仍然具有旧的角色/权限。

您或您的团队如何处理这种情况，以便在客户端应用程序中更新用户授权？

我已经看到了很多选择:

*   创建一个令牌版本，并在服务器端更新它，以便与新请求进行比较
*   保持令牌生命周期短
*   使用刷新令牌机制并使用户的当前令牌无效

我想听你说话...