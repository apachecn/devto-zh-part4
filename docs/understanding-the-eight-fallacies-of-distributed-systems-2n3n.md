# 理解分布式系统的八大谬误

> 原文：<https://dev.to/vaibsgharge/understanding-the-eight-fallacies-of-distributed-systems-2n3n>

这篇文章最初发表于[blog.vaibhavgharge.com](https://blog.vaibhavgharge.com/)

几十年来，软件行业一直在编写分布式系统。1969 年，美国国防部创建了阿帕网，也就是今天互联网的前身。大约在同一时间，SWIFT 协议建立了。

SWIFT 是一个庞大的报文传送网络，被银行和其他金融机构用来快速、准确、安全地发送和接收信息，如汇款指令。

然而，在 1994 年，当时的 sun 研究员 Peter Deutsch 起草了分布式系统的架构师和设计者可能会做出的 7 个假设，这些假设从长远来看被证明是错误的，给解决方案和做出假设的架构师带来了各种各样的麻烦和痛苦。

1997 年，詹姆斯·高斯林增加了另一个这样的谬论。这些假设现在被统称为**“分布式计算的八大谬误”**:

1.  网络是可靠的。
2.  延迟为零。
3.  带宽是无限的。
4.  网络很安全。
5.  拓扑不会改变。
6.  有一个管理员。
7.  运输成本为零。
8.  网络是同构的。

本文的目标是定义和澄清**“分布式计算的谬误”**的不同方面。

让我们仔细看看这些谬误，解释它们，并检查它们与当今分布式系统的相关性。

### 1。网络是可靠的

*   你上一次看到交换机故障是什么时候？毕竟，如今即使是基本的交换机，其 MTBFs(平均故障间隔时间)也在 50，000 小时以上。

*   如果您的应用程序是 365 x 7 任务关键型应用程序，您将会遇到这种故障 Murphy 将确保它发生在最不合适的时刻。

你可能会认为，大多数应用程序不是这样的。那么问题出在哪里？

*   嗯，有很多问题:电源故障，有人被网线绊倒，突然客户端无线连接，等等。如果硬件不足以让你的系统失控，那么软件也会失灵，而且确实如此。

*   如果您与外部合作伙伴协作，例如与外部信用卡处理服务一起工作的电子商务应用程序，这种情况甚至可能会令人不知所措。他们的连接不在你的直接控制之下，他们的失败会使你的应用程序不可用。

最后，还有 DDOS 攻击等安全威胁。

#### 那么如何处理这样的情况呢？

> 在基础设施方面，您需要考虑硬件和软件冗余，并权衡失败风险和所需投资。
> 
> 在软件方面，每当您通过线路发送消息/打电话时，您需要考虑消息/电话丢失的问题。在发展中使用断路器模式，给失败的一方一些喘息和恢复的时间。

总而言之，网络是不可靠的，我们作为软件架构师/设计师/开发者需要解决这个问题。

### 2。延迟为零

> 延迟只不过是数据从一个地方移动到另一个地方所花费的时间(相对于带宽，即在此期间我们可以传输多少数据)。

在局域网上，延迟可能相对较好——但是当您转移到广域网场景或互联网场景时，延迟会迅速恶化。

延迟比带宽更成问题。Ingo Rammer 在一篇关于延迟与带宽的文章中引用了一段话来说明这一点:

> “但我认为，在过去 11 年中，端到端带宽增加了 1468 倍，而延迟(单次 ping 操作所需的时间)仅提高了 10 倍，这真的很有意思。如果这还不够，甚至还有一个自然的延迟上限。这个地球两点之间的最短往返时间，是由信息传递的最大速度:光速决定的。以大约每秒 30 万公里(每两周 3.6 * 10E12 太埃)的速度，从欧洲向美国发送一个 ping 信号至少需要 30 毫秒，即使处理是实时进行的。”

#### 那么在这样的场景下能做些什么呢？

*   考虑延迟意味着你应该尽可能少地打电话，并假设你有足够的带宽。例如，AJAX/Asynchronous 方法允许使用用户消化数据的停滞时间来检索更多的数据——但是，您仍然需要考虑延迟。

*   就数据可用性做出正确的设计决策。您的应用程序能否从一天结束时的批处理过程中获取数据，从而避免网络调用以满足实时数据需求？

*   这些设计选择会对应用程序的整体用户体验产生巨大影响。

### 3。带宽是无限的

在我看来，这个谬论没有其他的谬论有力。如果说有什么东西在网络方面不断变得更好，那就是带宽。

然而，有两种力量在起作用，使这一假设成为谬误。

1.  一是随着带宽的增长，我们试图从中挤出的信息量也在增长。VoIP、视频和 IPTV 是占用带宽的一些较新的应用。下载、更丰富的用户界面、大的 javascript 包和对冗长格式(XML)的依赖也在起作用——尤其是当你使用较低的代码行时。

2.  降低带宽的另一个原因是丢包。在局域网或内部网环境中，丢包通常足够小。然而，另一方面，在广域网中，分组丢失通常相当大，并且是终端系统无法控制的。在流媒体和在线游戏应用中，丢包会严重影响用户的体验质量。

这主要意味着要考虑到在我们应用程序的生产环境中，可能会有超出我们控制范围的带宽问题。我们应该记住有多少数据会通过网络传输。

### 4。网络是安全的

*   Aladdin.com 公布的统计数据显示:“对于 52%的网络来说，边界是唯一的防御”。根据 Preventsys 和 Qualys 的调查，52%的首席信息安全官承认对其整体网络安全采取了“护城河和城堡”的方法。他们承认，一旦外围安全被攻破，他们的网络就会面临风险。

*   阿拉丁还声称 2004 年恶意软件(病毒、蠕虫、木马等)的成本。)估计在 1690 亿美元到 2040 亿美元之间。网络(不)安全的含义是显而易见的——您需要从第一天起就将安全性构建到您的解决方案中。

> 安全性是一个系统质量属性，需要从架构级别开始考虑。安全性通常是一个多层解决方案，在网络、基础设施和应用程序级别进行处理。

### 5。拓扑不会改变

*   第五个分布式计算谬论是关于网络拓扑。“拓扑不会改变。”没错，它不会——只要它还在实验室里。

*   当您在野外部署应用程序时(即部署到一个组织)，网络拓扑通常不在您的控制范围内。运营团队(IT)
    可能会偶尔添加和删除服务器，和/或对网络进行其他更改。

*   当你谈论客户时，情况就更糟了。笔记本电脑来来往往，无线特设网络，新的移动设备。简而言之，拓扑是不断变化的。

*   这对我们开发的应用程序意味着什么？简单。如果您无法准备好重新协商端点，请不要依赖于特定的端点或路由。或者提供发现服务，允许抽象网络的物理结构并适当地处理它。

*   最明显的例子是 **DNS** 名称而不是 IP 地址。例如，如果你把你的博客网站从一个主机服务转移到另一个。迁移将顺利进行，因为当 DNS 路由表被更新时(需要一两天的时间来改变)，读者将被引导到新的站点，而不知道他们脚下的路由(拓扑)已经改变。

### 6。有一个管理员

*   IT 团队通常有不同的管理员，根据专业知识分配——数据库、web 服务器、网络、Linux、Windows、主机等等。这是简单的情况。

*   当您的公司与外部实体协作时(例如，与业务合作伙伴连接)，或者如果您的应用程序是为 Internet 消费而部署的，并且由某个托管服务托管，并且该应用程序消费外部服务，就会出现问题。在这些情况下，其他管理员甚至不受您的控制，他们可能有自己的议程/规则。

*   好吧，只要一切正常，你可以不在乎。然而，当事情误入歧途，需要找出问题(并解决它)时，你会在意。此外，你需要理解管理员很可能不是你开发团队的一部分，所以我们需要给他们提供工具来诊断和发现问题。

*   总而言之，当有多个管理员时，您需要记住管理员可以约束您的选项(设置有限权限、有限端口和协议等的管理员)，并且您需要帮助他们管理您的应用程序。

### 7。运输成本为零

你可以用几种方式来解释这句话，这两种方式都是错误的假设。

1.  一种方法是从应用层到传输层是免费的。这是一个谬误，因为我们必须进行编组(将信息序列化为比特)才能将数据传输到网络上，这既占用了计算机资源，又增加了延迟。

2.  对这句话的第二种解释是，设置和运行网络的费用(如现金)是免费的。这也远非事实。有成本——购买路由器的成本，保护网络的成本，租用互联网连接带宽的成本，以及运营和维护网络运行的成本。某个地方的某个人将不得不为这些费用买单。

### 8。网络是同构的

*   第八个也是最后一个分布式计算谬误是“网络是同质的”，这是詹姆斯·高斯林六年后(1997 年)加上去的。任何网络，除了可能非常琐碎的网络，都不是同质的。见鬼，甚至我的家庭网络也有一台基于 Linux 的 HTPC，几台基于 Windows 的个人电脑和一台 Android 手机——所有这些都通过无线网络连接在一起。

*   即使你设法保持了内部网络的同质性，当你试图与合作伙伴或供应商合作时，你也会遇到这个问题。

*   值得注意的是，在应用程序级别上，网络并不是同质的。这意味着您必须假设迟早会需要互操作性，并从第一天起就准备好支持它。

*   不要依赖专有协议——以后集成它们会更加困难。使用被广泛接受的标准技术；最显著的例子是 XML 或 Web 服务。

### 结论

*   即使在今天，云服务的崩溃或“中断”也惊人地频繁发生。当您计划或开发分布式应用程序时，假设您的网络中不一定存在的属性和质量是一个坏主意:更好的做法是假设您的网络将是昂贵的，并且偶尔会不可靠和不安全。
*   请记住,(成功的)应用程序会不断发展壮大，所以即使事情暂时看起来没问题，如果你不注意谬误所掩盖的问题，它们也会抬起丑陋的头来咬你一口。
*   我希望阅读这篇文章既有助于解释这些谬论的含义，又能为如何避免它们的影响提供一些指导。

希望这篇文章对你有用。请在评论区分享你的想法。

我很乐意谈谈！如果你喜欢这篇文章，请分享，评论，给一些👏 😊干杯。下次见。

##### 参考文献

*   IT 架构和中间件，C. Britton，Addison-Wesley 2001，ISBN 0-201-70907-4 [JDJ2004]。[http://java.sys-con.com/read/38665.htm](http://java.sys-con.com/read/38665.htm)
*   http://blogs.sun.com/roller/page/jag
*   【Ingo】[http://blogs . think tecture . com/Ingo/archive/2005/11/08/latencyvsbandwidth . aspx](http://blogs.thinktecture.com/ingo/archive/2005/11/08/LatencyVsBandwidth.aspx)
*   [rich ui][http://rich ui . blogspot . com/2005/09/Ajax-latency-problems-myth-or-reality . html](http://richui.blogspot.com/2005/09/ajax-latency-problems-myth-or-reality.html)