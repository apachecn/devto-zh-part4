# 像老板一样向左推—第 5.7 部分— URL 参数

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-7-url-parameters-4ajc>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/g8tg)！

永远不要将*重要的*信息放在应用程序 URL 的参数中。当我说“重要”时，我指的是在您的应用程序中可能用于做出决策的东西，而不是无关紧要的东西。隐藏字段也是一样，也不要在那里存储或传递任何有价值的东西。重要的信息必须以安全的方式传输，隐藏字段和 URL 参数不适合这样做。

将敏感信息放入 URL 的风险包括:敏感数据被缓存，敏感数据在中间人攻击的情况下暴露，或者攻击者可能注入他们自己的值。

不应出现在 URL 参数中的内容示例:

*   用户 id(用于登录系统的用户，不用于标记公共页面，仅此而已。公共页面上的书签不敏感。)
*   账号
*   SIN 数
*   出生日期和其他可能用来冒充某人的信息组合
*   标识地址
*   查询或搜索信息
*   个人身份信息(PII)
*   令牌或会话 ID

[![Franziska Bühler and I at the Open Security Summit, 2018](img/979da16cf77e4b4e15b42eec1734f8b2.png "Franziska Bühler and I at the Open Security Summit, 2018")](https://res.cloudinary.com/practicaldev/image/fetch/s--dM38jj2t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lzvl4qvppllwg5g8h548.jpeg)

###### [弗兰齐斯卡·布勒](https://twitter.com/bufrasch)和我在 2018 年[开放安全峰会](https://open-security-summit.org/)

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/lvv4)！