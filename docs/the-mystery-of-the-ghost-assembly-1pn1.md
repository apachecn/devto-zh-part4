# 解决中的“找不到类型或命名空间”问题。网

> 原文：<https://dev.to/bellonedavide/the-mystery-of-the-ghost-assembly-1pn1>

今天发生了一些奇怪的事情。

我开发了一个类库。NET 框架，我用相关的测试库进行了测试。

所以我把这个库整合到了另一个项目中。一切都很好，我使用图书馆没有任何问题。
但是，突然:

错误 CS0246:找不到类型或命名空间名称' XX '(您是否缺少 using 指令或程序集引用？).

**什么？！？**我已经用了整整一周了。我测试过了。我已经添加了引用，智能感知也可以工作。

来吧，班级在这里！

清洗溶液...什么都没发生。

关闭 Visual Studio...还是一无所获。

喝杯咖啡...现在好多了。

突然，一个疑问:*如果我在。NET Core 而不是。NET 框架？*

很明显...不，不是那样的。

但是我很接近了:类库在*里。NET Framework 4.7.2* 但是应用在*。NET Framework 4.7.1* 。**那是补丁版本！**

于是，我就通过 Visual Studio 把类库版本降级了，一切又恢复了正常。

您可以在项目的属性屏幕中找到该版本(在解决方案浏览器中右键单击该项目或者在*应用程序*选项卡中的*目标框架*字段下的 *Alt+Enter* )。

[![.NET version selection on Visual Studio 2019](img/eac0f2186af69ee556141f3cb208b0d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8w01v5_N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bellons/image/upload/Code4IT/TypeOrNamespace%2520not%2520found/dotnet-version-selector.png)

所以，教训就是:*知己知彼*，察微知著。