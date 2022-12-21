# 像老板一样向左推—第 5.11 部分—授权(AuthZ)

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-11-authorization-authz-4el5>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/g8tg)！

* * *

授权(也称为“AuthZ”)是验证试图在您的应用程序中执行某个操作的用户是否被允许(被授权/拥有权限)使用该功能。例如，用户是管理员用户吗？如果是，允许他们查看管理页面。否则，阻止访问。

在我们的行业中，有几种不同的授权模型，其中 RBAC(基于角色的访问控制)是最流行的。RBAC 意味着在您的系统中为人们分配不同的角色，就像人们在您的组织中扮演不同的角色一样，并根据他们被分配的角色授予他们访问权限。

例如，认识一下 Emily，一个假想的软件开发人员，她是我的项目团队中的新人(如下图)。

[![#WOCinTechChat: Emily the Software Developer](img/fb5eef3c517b5802a60fcdee8b3e053b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YJg2UDpV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/klexnltrg6qrkz6mjvyk.png)

###### 软件开发员艾米丽

作为一名软件开发人员，她需要接触各种各样的东西；源代码控制，也许是发布到 CD/CI 管道的许可，以及各种文件系统。

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/b0uv)！