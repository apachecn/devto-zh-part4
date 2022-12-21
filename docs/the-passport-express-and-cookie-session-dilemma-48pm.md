# 护照、快递和曲奇的困境。

> 原文：<https://dev.to/emmymay/the-passport-express-and-cookie-session-dilemma-48pm>

我最近决定中途转用 PHP 和 MariaDB SQL，转用 NodeJs 和 MongoDB。我已经建立了我的第一个 CRUD 应用程序，所以我已经弄清楚了节点如何与数据库交互。

现在，我开始讨论身份验证，我决定从 oauth2.0 开始，完全使用 Google 的。

问题是，我认为我已经做了所有正确的事情，但我的应用程序没有进入“已登录”状态，这是因为它在浏览器中保存 cookie，但我认为浏览器没有将这些 cookie 返回到服务器，所以我从未被检测到“已登录”。

我会给你留一个原文的链接。对不起我刚接触 DEV.to，还不了解它的编辑器。

下面是链接:[https://hash node . com/post/the-passport-express-and-cookie-session-dilemma-CJ xx 77s 3 u 0009 w 1 S1 LSI 9 c 6 gj](https://hashnode.com/post/the-passport-express-and-cookie-session-dilemma-cjxx77s3u0009w1s1lsi9c6gj)