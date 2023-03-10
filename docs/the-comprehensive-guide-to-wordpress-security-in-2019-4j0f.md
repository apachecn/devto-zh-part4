# 2019 年 WordPress 安全综合指南

> 原文：<https://dev.to/lizziekardon/the-comprehensive-guide-to-wordpress-security-in-2019-4j0f>

这个 WordPress 安全指南有两个好处:

*   **准确了解 2019 年你需要知道的关于 WordPress 安全的事情。**了解 WordPress 的安全性有助于你采取以安全为导向的心态，这将帮助你在做日常决策时预防和减少风险。
*   获得保护你的 WordPress 站点的可行的、逐步的指导。您需要采取的步骤并不特别耗时，不需要高深的技术知识，并且链接的指南经过了清晰和完整的审核。

当然，不可能涵盖所有可能的漏洞和场景。这就是为什么我们也包括了首要的“WordPress 安全原则”如果你能遵循这些宽泛的原则，许多在这类文章中难以解决的细节问题就会迎刃而解。

## 为什么 WordPress 的安全性至关重要？

理解 WordPress 的安全性是一个多么大的问题是很重要的，所以你应该给它足够的时间和关注。以下是一些发人深省的统计数据:

*   谷歌每天将 1 万个网站列入恶意软件黑名单，每周将另外 5 万个网站列入网络钓鱼黑名单。谷歌的黑名单扼杀了这一流量来源，而这可能会对企业的收入造成极大的破坏。修复和删除黑名单可能会很麻烦，尤其是当你的服务器被用来发送垃圾邮件的时候。来自电子邮件服务提供商的黑名单会损害你的投递率，从而降低你的电子邮件营销的有效性。
*   [2018 年，90%的 CMS 黑客都是 WordPress。](https://www.zdnet.com/article/wordpress-accounted-for-90-percent-of-all-hacked-cms-sites-in-2018/)诚然，WordPress 是世界上最受欢迎的内容管理系统， [34%的网页由 WordPress](https://w3techs.com/technologies/details/cm-wordpress/all/all) 提供支持，但这仍然是一个不成比例的黑客数量。出于各种原因，WordPress 网站比任何其他 CMS 更容易被攻击和利用。
*   [60%的攻击以小型企业为目标。你可能认为黑客不会打扰你的网站，因为你是一条小鱼，但是“通过模糊来获得安全”在网络上是不存在的。没有人会因为太小而成为攻击的受害者。](https://www.inc.com/joe-galvin/60-percent-of-small-businesses-fold-within-6-months-of-a-cyber-attack-heres-how-to-protect-yourself.html)
*   据 WordFence 报道，每分钟有超过 97，978 起攻击发生。这不是一个小的、孤立的问题。大量的 WordPress 网站在任何时候都在被探测弱点。如果你的网站被攻击，你准备好了吗？

虽然这些统计数据描绘了一幅你所面临的令人生畏的画面，但重要的是你要面对 2019 年管理 WordPress 网站的现实。

如果你的业务依赖 WordPress，安全是你不能忽视的。如果你没有采取措施来保护你的网站，你很容易受到攻击。你的网站被入侵只是时间问题。这是一个“一盎司预防抵得上一磅治疗”的领域

## WordPress 安全原则

保护 WordPress 不仅仅是检查一系列的框，它需要你采取一种安全导向的心态来指导你的决策。这些原则将帮助你在日常决策中避免错误，比如添加新的 WordPress 用户、插件和主题:

### WordPress 用户经常引入漏洞。

保持更新并使用强密码的普通 WordPress 是相对安全的。通常是 WordPress 用户做出的决定造成了黑客可以利用的弱点。这就是为什么像这样的文章如此重要。这里列出的 WordPress 安全最佳实践，由你来接受教育，并始终如一地遵循。

### 最小化插件和主题。

Wordpress 的新用户经常对 WordPress 广泛的插件和主题生态系统欣喜若狂。这可能会导致不加选择地安装插件。但是，每一个新的插件或主题都会给你的网站增加代码，潜在地增加新的漏洞。通过最小化安装的插件和主题，你也最小化了你的站点使用的代码量，并且，通过扩展，潜在的安全漏洞的数量。一般来说，如果你不需要安装插件就能解决问题，那是最好的选择。

### 尽快安装更新。

WordPress 核心更新以及插件和主题更新通常包含重要的安全更新。应该尽快安装它们。如果您有一个好的备份策略，当更新导致问题时，您可以回滚到以前的版本。

### 遵循最小特权的[原则。](https://en.wikipedia.org/wiki/Principle_of_least_privilege)

这是一般网络安全的核心原则，并不是 WordPress 独有的。这是一种奇特的说法，“不要给用户比他们需要的更多的特权，因为这些额外的特权给了黑客更多的权力和访问权限，如果他们危及帐户安全的话。”

### 攻击仍然会发生。

你可以尽最大努力覆盖所有的基地，但仍然会成为攻击的受害者。这就是定期自动备份至关重要的原因。不要忽视这个建议。被黑客攻击的站点可能会造成破坏，但通常可以用最少的成本和破坏来修复，没有备份的被黑客攻击的站点可能会造成严重的破坏。

### 你永远不会结束安全。

拥有一个 100%安全的 WordPress 网站是不可能的任务。你不可能完全消除风险，所以要以持续降低风险为目标。这种“降低风险”的心态促使您继续采取措施来提高您的安全性，作为一个持续的、永无止境的计划的一部分。

## 最常见的 WordPress 攻击媒介

了解黑客利用 WordPress 最常用的方法将有助于你理解如何解决这些问题，以及为什么本文后面推荐的步骤是必要的:

### 暴力攻击

网络罪犯使用试错法来识别密码或 pin。他们使用常用用户名和密码的组合，直到找到正确的组合。这就相当于尝试钥匙圈上的每一把钥匙来找到一把能用的。这都是用计算机脚本完成的，所以他们可以不费吹灰之力就运行成千上万的组合。只要有足够的时间，任何帐户都可以被黑客攻击，但是强密码可以阻止这种攻击。

### SQL 注入

恶意的 SQL 代码被注入到数据库中，以获得对从不打算显示的数据库信息的访问。根据黑客的目标，这可能会导致您的数据库被删除、客户信息被窃取或敏感的公司信息被访问。

### 恶意软件

使用过期的主题或插件插入病毒或间谍软件。攻击者可以访问您的数据，将页面插入您的站点进行黑帽 SEO，并使用您的站点执行许多其他恶意活动。

### 跨站点脚本

插入 Javascript 代码，然后收集通常用于利用 WordPress 插件的数据。这可用于将您的站点访问者重定向到恶意内容。也被称为“XSS 袭击”

### DDoS 攻击

分布式拒绝服务(DDoS)攻击使网站流量泛滥，使服务器资源不堪重负，导致网站瘫痪。多台机器使用安装在这些机器上的恶意软件向服务器频繁发送请求。攻击的分布式特性使得识别和阻止流量来源变得非常困难。

既然您已经了解了黑客是如何试图破坏您的网站安全的，那么让我们来看看如何阻止和防止这些攻击…

## 保护你的 WordPress 站点的步骤

尽管您的站点可能会受到多种方式的危害，但减轻这些风险并防止许多攻击是可能的。以下是保护你的 WordPress 网站免受安全攻击的建议步骤:

### 1.强制使用强密码

" [81%的攻击都是基于不安全或被盗的密码](https://www.pandasecurity.com/mediacenter/adaptive-defense/most-common-tactics-among-cybercriminals/)"

破解一个 6 个字符长的小写密码只需要大约 10 分钟。

这两个事实加在一起，说明你必须为所有 Wordpress 用户使用强密码。强密码很长，使用大小写字母、数字和符号的组合。越长越好，但 20 个字符可能就足够了。

强密码很难记住，尤其是如果你为每个网站使用唯一的密码(你应该记住！).这意味着密码管理器，如 [LastPass](https://lastpass.com) 或 [Dashlane](https://dashlane.com) ，是一个关键的工具。这些应用程序使创建强密码变得容易，将它们存储在一个中心位置，并通过自动填充密码使登录网站变得容易。

以下是如何确保你所有的 WordPress 用户都使用强密码。

### 2.打开自动 Wordpress 核心更新

使用像 WordPress 这样受欢迎的 CMS 的一个好处是，有很多人对保持它的安全性有既得利益。成千上万的人通过向 WordPress 团队报告漏洞为 WordPress 安全做出了贡献。这种广泛的合作意味着大多数漏洞都能相对快速地得到修补。但是，如果不应用包含这些修复的更新，您的站点就容易受到攻击。

确保 WordPress 核心更新快速发生的最好方法是打开自动更新。这样你的 WordPress 站点将会保持最新，而你不需要做任何事情。

以下是如何通过修改您的 config.php 文件来打开自动核心更新。

### 3.保持插件和主题更新

你必须保持你的插件和主题是最新的。这一点的重要性怎么强调都不为过。

你也要小心使用经常更新的插件和主题。如果你使用的插件或主题已经几个月没有更新了，最好去找开发者或者找一个更新更频繁的插件来实现同样的目标。

当你在 WordPress 插件数据库中搜索一个插件时，这个信息在侧边栏中提供:

[![Avoid plugins that have not been updated recently](img/a126164a1f3eb8274c38851b148d141e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--McfFz57u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pagely.com/wp-content/uploads/2019/08/Image-2019-08-02-at-4.03.59-PM.png)

### 4.使用双因素身份验证(2FA)

双重认证给你的 WordPress 站点增加了一层保护，黑客几乎不可能登录你的站点——即使他们知道你的用户名和密码。

你应该为所有 WordPress 用户设置 2FA。2FA 可以通过简单的免费插件快速安装。

这里是 WordPress 可用的 2FA 插件解决方案列表。

### 5.强力保护

即使有 2FA 和强密码，通过减少 PHP 必须做的工作量，设置暴力保护有助于服务器的整体性能。

以下是如何保护你的网站免受暴力攻击的方法。

### 6.创建自动化的定时备份

如果出现问题，应该总是有最新的数据库和文件备份可用。你也应该在对你的站点做任何重大改变之前创建一个备份，比如安装插件和主题更新。许多流行的备份解决方案会为您解决这一问题。

[这是你的 WordPress 站点的备份解决方案列表](https://www.isitwp.com/best-wordpress-backup-plugins-compared/)

### 7.更改您的登录页面

所有 WordPress 站点的默认登录页面都是 sitename.com/wp-login.php 和 sitename.com/wp-admin.php.。使用这些默认登录页面 URL 使得黑客很容易通过尝试用户名和密码的组合来开始暴力攻击。通过使用难以猜测的登录 URL，黑客开始暴力攻击变得更加困难，因为他们不知道登录表单位于何处。

以下是如何更改您的登录页面网址。

### 8.使用 SSL

安全套接字层(SSL)加密从您的网站发送到访问者浏览器的所有数据，隐藏潜在的敏感数据。除了安全性之外，使用 SSL 还有很多好处。谷歌可能会在搜索结果中将你的网站排在更高的位置，许多浏览器会显示一个网站是否受到 SSL 的保护，这可以让他们确信你的网站是值得信赖的。

这里有一个关于为你的 WordPress 站点设置 SSL 的教程

### 9.不要使用默认的数据库前缀

使用默认的“wp_”数据库前缀使得黑客更容易将代码插入到你的数据库中(SQL 注入)。

以下是如何为新站点和现有站点更改默认数据库前缀的方法。

### 10.检查您的文件夹和文件权限

文件权限定义了可以对服务器上的文件应用哪些操作。你不应该将任何 WordPress 文件或目录设置为 777 权限。

相反，请确保您的权限方案如下所示:

文件夹–755 个文件–644 个

这里有一个检查和修复 WordPress 文件和文件夹权限的指南。

### 11.禁用 pingbacks

默认情况下，WordPress pingbacks 是启用的。但是对于大多数网站来说，他们提供的好处很少。但是，它们可以被用来把你的 WordPress 站点变成一个不情愿的 DDoS 僵尸网络的参与者。

要关闭 pingbacks，您可以进入“设置–>讨论”并取消选中“尝试通知文章链接到的任何博客”框。取消选中“允许来自其他博客(pingbacks 和 trackbacks)的关于新文章的链接通知”框也是一个好主意:

[![Disable WordPress pingbacks to improve security](img/96d87b41a8d0b3fe86e11e27df8487db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iNiGFjZu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pagely.com/wp-content/uploads/2019/08/wordpress-security-pingbacks.png)

### 12.隐藏你的 WordPress 版本号

攻击者获得网站访问权的方法之一是利用 WordPress 过时版本中的已知漏洞。黑客可以扫描你的网站，很容易发现你使用的 WordPress 是什么版本。如果这个版本有一个已知的漏洞，那么黑客有一个好主意如何进行。

防止这种情况的最好方法是保持 WordPress 最新。然而，你也可以防止黑客发现你运行的是什么版本的 WordPress。

以下是如何隐藏你的 WordPress 版本号。

### 13.不要使用管理员用户名

黑客可以安全地假设 WordPress 站点使用默认的“admin”用户名，这个用户名会给他们管理员权限，所以他们需要做的就是找出密码。这给了他们一个直接的途径来获得你的网站的管理员权限，成功的可能性很大。取消管理员用户名会使以这种方式攻击您的网站变得更加困难。如果您已经在使用管理员用户名，没关系，您现在可以更改它。

下面是更改管理员用户名的方法。

### 14.使用一个[安全管理的 WordPress 主机](https://pagely.com/solutions/secure-wordpress-hosting/)

一个支持所有主要 CMS 的主机将很难保持 WordPress 的安全性。通过专门研究 WordPress CMS，WordPress 主机提供商可以投入必要的资源来防止许多攻击。

一个有声望的[管理的 WordPress 主机](https://pagely.com/blog/managed-wordpress-hosting/)会有一个安全团队专门保护你的网站免受威胁。他们将处理上面提到的许多问题，比如更新 WordPress、创建备份等等。在 Pagely，我们有一套全面的安全功能，叫做 [PressArmor](https://support.pagely.com/hc/en-us/sections/114094016512-Security-PressARMOR-) ，处理 WordPress 应用程序安全和服务器安全。

问潜在的托管 WordPress 主机的安全问题:

您的托管环境是否已升级？如果你的主机在同一个服务器上有多个域，请与他们核实，确保他们使用 chroot 来隔离每个 WordPress 应用程序。如果一个应用程序被黑客攻击，这将有助于防止访问服务器上的其他应用程序。

**是否包含 SSL 并且易于设置？一个好的 WordPress 主机提供商会使用像 Let's Encrypt 这样的服务免费提供 SSL。**

你会主动监控我的网站上的 WordPress 漏洞吗？如果您发现我的网站存在漏洞，您将如何通知我？一个好的 WordPress 主机将保持在 WordPress 网站的最新威胁之上，并不断扫描你的网站寻找这些威胁。如果他们发现了一个漏洞，他们会和你一起解决它。

您是否使用网络应用防火墙(WAF)来保护我的网站？WAF 可在恶意流量到达您的站点之前将其拦截，并有助于防止 XSS 攻击和 SQL 注入。

这里列出了选择托管 WordPress 主机的重要考虑因素。

## 结论

WordPress 的安全性不能掉以轻心，你必须采取措施保护你的网站。使用像 [Pagely](https://pagely.com) 这样的托管服务提供商是提高安全性的最好方法之一，因为你会有一个经验丰富的团队专门保护你。

无论你是选择自己处理 WordPress 安全问题，还是利用 WordPress 的专业主机，将 WordPress 安全视为一项持续的努力是很重要的。您永远不会放弃安全性，但是如果您采取了上述步骤，您就可以保护自己免受许多最常见的威胁。