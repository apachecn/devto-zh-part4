# 解决“登录 _ 必须 _ 使用 _ 安全 _ 令牌”错误

> 原文：<https://dev.to/rachelsoderberg/salesforce-series-part-6-resolving-loginmustusesecuritytoken-error-1of4>

本周，我遇到了一个以前从未遇到过的新的 Salesforce 问题——我完成了一个项目的开发，这个项目我已经在工作中构建了好几个星期，很高兴看到它在测试服务器上运行。会出什么问题呢？！良好的...实际上是几件事。大多数都是小问题，比如确保我在 web.config 文件中将 Salesforce API 和连接信息设置为正确的版本，但是一次令人讨厌的挂断让我和我的经理整整困惑了几天！

我最终缩小了日志和错误消息的范围，直到能够找到问题代码的确切部分；这允许我在代码部分中生成以下 SoapException，其中如果应用程序尚未登录，我使用 Salesforce 绑定登录到我们的组织:

 *异常:系统。web . services . protocols . soap exception:log in _ MUST _ USE _ SECURITY _ TOKEN:用户名、密码、安全令牌无效。或者用户被锁定。你在一个新的位置吗？当通过桌面客户端或 API 从您公司的受信任网络外部访问 Salesforce 时，您必须向您的密码添加安全令牌才能登录。要获取您的新安全令牌，请登录 Salesforce。从您的个人设置中，在快速查找框中输入重置我的安全令牌，然后选择重置我的安全令牌。
荀攸制。web . services . protocols . soaphttpclientprotocol . read response(SoapClientMessage message，WebResponse response，Stream responseStream，Boolean asyncCall)
at System。web . services . protocols . soaphttpclientprotocol . invoke(String method name，Object[]parameters)
at boxsalesforcelistener。Salesforce.SforceService.login(字符串用户名，字符串密码)
在 BoxxSalesforceListener。RMAUpdateService . checkifloggedintosaleforceandlogininfot()* 

一旦我们看到这个错误代码，问题就变得如此明显。我们不使用安全令牌登录(尽管如果您想要提供对被您的组织视为“不可信”的应用程序的访问，这是一个选项)。安全令牌只是您可以让您的应用程序在登录到 Salesforce 时输入的额外凭证。

我们没有添加代码来提供安全令牌，而是选择修改我们的 Salesforce 组织中的可信网络列表。这个过程也很简单:

1.  登录到您的问题 Salesforce 组织(可能是生产或沙盒)
2.  点按“设置”，然后搜索“网络访问”或在“管理”>“安全控制”下找到它
3.  获取服务器的 IP 地址*
4.  输入服务器的 IP 地址作为新的可信 IP 范围，提供起点和终点，或者在两个字段中使用单个地址

就这样，问题应该解决了！您的服务器现在被视为与您的 Salesforce 组织的可信连接，应被允许仅使用用户名和密码凭据进行连接。如果您添加更多服务器，您必须对访问您的 Salesforce 组织的每个新服务器进行此更改，因此最好记住这一点，不要浪费两天的工作(像我一样...yeesh)。

*如果你不熟悉如何获得你的 IP 地址，至少有两种方法可以获得:从服务器打开一个 cmd 窗口，输入不带引号的“ping servername ”,使用你的服务器名而不是 servername。第二个选项是从服务器上的网络浏览器导航到[https://whatismyipaddress.com/](https://whatismyipaddress.com/)。

* * *

你有什么关于 Salesforce 的话题想让我研究和写吗？我总是在寻找新的东西来钻研和学习，Salesforce 给了我很多乐趣！