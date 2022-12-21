# 谷歌助手现在支持“打开/关闭”设备

> 原文：<https://dev.to/samthor/google-assistant-now-supports-open-close-devices-2512>

自从我写了这篇关于使用谷歌助手打开和关闭我的车库门的文章后，谷歌已经检查并增加了一些设备类型。🆕

去看看这个帖子吧！...TL；大卫:在我家，你可以说“好的，谷歌，打开车库门”，门就会打开。🏡🎉

## iftt 命令

当时，我用如果-这个-然后-那个。IFTTT 很简单，它只是在你的 Google Assistant“个人资料”中添加随机字符串就可以匹配。此外，如果你的 Google Home 设备上有多个用户(在我的例子中就是这样:我自己和我的伙伴)，其他任何人也可以触发这些命令。

因此，使用 IFTTT，您可以添加一个命令，比如“Ok Google，do the dishes ”,它调用 REST API 或 IFTTT 可以做的任何事情。但是 IFTTT 的主要缺点是它没有真正的“失败”概念:如果您的 REST API 返回一个 404/500 等，IFTTT 没有真正的方法将它通过管道返回给您，它只会说一个固定的字符串。

## 辅助设备

对于设备“正确地”连接到你的助手账户的更复杂的方法(想想完全融入其生态系统的灯泡)，谷歌还不支持“打开”或“关闭”动词。

一些人创建了一些帖子，谈论如何通过说“好的，谷歌，打开我的东西”或“打开我的东西”来控制你的门...东西掉了”(假装是一个*智能插头*)，但是动词没有意义。

# 计划

因此，在接下来的几天里，我将研究如何模拟一个谷歌知道的真实设备，从我的门上移除 IFTTT 支持，但模拟一个连接的设备。这是可能的，因为 Google 现在支持:

*   [车库门类型](https://developers.google.com/actions/smarthome/guides/garage)
*   以及[打开关闭特性](https://developers.google.com/actions/smarthome/traits/openclose)。

Fifteen