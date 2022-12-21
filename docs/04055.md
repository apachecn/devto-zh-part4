# 像老板一样向左推—第 5.10 部分—不可信数据

> 原文：<https://dev.to/shehackspurple/pushing-left-like-a-boss-part-5-10-untrusted-data-5d92>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/g8tg)！

* * *

#### 信任数据来自…没有人。不是数据库，不是 API，甚至不是你妈妈。

[![Sydney, Australia, at #MSIginiteTheTour — I’m the tiny dot at the front](img/e25223e43ba3ed9f20c5fae6032bf69f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eBoMdg9L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1vn301ef2amynejs3kq0.jpeg)

###### 我在澳大利亚悉尼参加#微软点火之旅。我是一个小点。

发送到您的应用程序的任何数据都需要被视为不可信的，因此在使用或保存之前需要进行验证。我这么说，是指**所有数据**。将数据保存到数据库的人可能在验证输入时出错。您调用的 API 可能已被破坏。即使是一个高智商的用户，比如我的母亲(拥有化学和数学两个学位，一个会计头衔，还有几个证书，包括成人教育——她非常*聪明。)，在使用应用程序时可能会犯一个简单的错误，比如输入单引号而不是双引号，这可能会使您的应用程序进入错误状态，导致崩溃或更糟。*

* * *

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/ht69)！

* * *