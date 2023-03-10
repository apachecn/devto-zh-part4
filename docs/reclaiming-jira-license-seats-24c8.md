# 回收吉拉牌照席位

> 原文：<https://dev.to/lbonanomi/reclaiming-jira-license-seats-24c8>

我的雇主的吉拉安装程序使用 Active Directory 连接器来维护用户数据库。我们的许可证将目录中出现的每一个启用了*的*用户算作一个席位，而不考虑应用程序权限或登录能力。让我们重写吉拉的 Active Directory 连接器规则，以更好地反映谁在真正使用该产品。

安装向导使用非常广泛的 LDAP 查询在 Active Directory 中查找吉拉用户:`(&(objectCategory=Person)(sAMAccountName=*))`。这使得设置变得简单，但代价是 Active directory 中的每个用户都吃掉了一个许可席位。由于内部 IT 和应用程序工程之间的公司孤岛，没有足够多的人知道作为人力资源入职的一部分，为每个用户创建了一个活动目录对象。

我们公司的信息安全政策要求密码在 90 天后过期，吉拉不允许带有“下次登录时更改密码”标志的用户登录。让我们假设 2019 年没有更改密码的用户可以在不立即访问吉拉的情况下生活，只需将他们的用户名完全从目录中删除即可。

Active Directory 以 LDAP 的时间格式将最后一次密码重置时间存储在字段`pwdLastSet`中(Windows 人员可能会将其识别为“NT 时间”)。[epochconverter.com](https://www.epochconverter.com/ldap)的好心人提供了一个将 wall time 转换成 LDAP 格式的计算器，让我把“2019 年 1 月 1 日 00:00:00”变成 LDAP 时间戳“131907744290000000”。

我们在 Active Directory 中查找用户的优化查询将类似于:

```
(&(objectCategory=Person)(pwdLastSet>=131907744290000000)(sAMAccountName=*)) 
```

更新一个测试服务器的目录显示，*在吉拉注册的用户有 40%在这次变更后烟消云散。*