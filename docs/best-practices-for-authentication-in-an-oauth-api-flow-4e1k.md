# oauth API 流中身份验证的最佳实践？

> 原文：<https://dev.to/imjacobclark/best-practices-for-authentication-in-an-oauth-api-flow-4e1k>

我正在构建一个应用程序，该应用程序使用 Spotifys API OAuth 进行授权，我的应用程序最终从 Spotify 收到的访问令牌作为 HttpOnly Cookie 存储在请求我的应用程序的客户端上，以便传递给后续的 API 请求。

以这种方式使用 API 时，确保身份验证的最佳实践是什么？例如，用户就是他们在后续请求中所说的人。