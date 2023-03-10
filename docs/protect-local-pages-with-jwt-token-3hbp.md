# 用 JWT 令牌保护本地页面

> 原文：<https://dev.to/tmblog/protect-local-pages-with-jwt-token-3hbp>

在本教程之后，我用 PHP 创建了一个 JWT。这很好，只要令牌没有过期，我就可以查看服务器上的其他页面。

我有这样一个场景:
**登录页面**，成功认证后创建一个 JWT 令牌。
**查看帖子页面**，通过传递 JWT 从服务器检索帖子。

我想知道如何保护这个**查看文章页面**不被直接访问，而不必创建本地会话。按照现在的情况，可以访问查看帖子页面，但是如果没有传递 JWT 令牌，将不会显示任何内容。

正常情况下，流程是在成功登录时在本地创建一个会话，并保持类似的状态，只是想知道是否有一种方法可以在本地使用 JWT 令牌。希望这有意义。如果我有什么安全考虑的话。
干杯