# 像老板一样向左推——第 5.8 部分——保护你的饼干

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-8-securing-your-cookies-ea2>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/g8tg)！

本系列的上一篇文章是 5.7 URL 参数。

继续我们对安全编码原则的长途跋涉，我们来到了 cookies 的话题，它用于在客户机和服务器之间来回发送信息。

为了保护我们需要在客户机和服务器之间传递的决策和/或敏感数据，我们需要放入一个安全的 cookie。安全 cookies 是经过**加密的**，而不是**编码的**，这意味着有人需要一个密钥来解密它们，改变或泄露它们包含的信息。对于安全 cookies，该密钥存储在服务器上(一个安全的位置)。任何敏感的、在应用程序中用于决策的、或者不适合放在 URL 参数或隐藏字段中的内容都应该在安全的 cookie 中传递。

[![My friend Imran A Mohammed and I, #Null Singapore 2019](img/d90f1f553df263492c723d6028db3711.png "My friend Imran A Mohammed and I, #Null Singapore 2019")](https://res.cloudinary.com/practicaldev/image/fetch/s--ddEV88Ud--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxa7igg27hx45nz1l3qp.jpeg)

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/wehy)！