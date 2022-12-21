# 如何将 Office 365 中的经典根网站集转换为现代 SharePoint

> 原文：<https://dev.to/mlisidoro/how-to-convert-your-classic-root-site-collection-in-office-365-to-modern-sharepoint-7jo>

帖子[如何将 Office 365 中的经典根网站集转换为现代 SharePoint](https://blogit.create.pt/miguelisidoro/2019/08/27/how-to-modernize-your-tenant-root-site-collection-in-office-365-using-invoke-spositeswap/) 首先出现在[博客 IT](https://blogit.create.pt) 上。

你有以经典团队网站为主页的旧 Office 365 租户吗？想要将传统的根网站集转换为现代的 SharePoint 网站吗？

现在，使用最近发布的[Invoke-spositewap](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/invoke-spositeswap?view=sharepoint-ps)PowerShell cmlet，您可以将传统租户的根站点集合转换为现代站点集合(例如现代通信站点)。

## 简介

今年早些时候，所有新的 Office 365 租户开始默认将现代通信网站作为根网站。不幸的是，大部分 Office 365 租户的年龄都比这大，他们仍然使用经典的团队网站作为根网站集。

到目前为止，还没有一种简单的方法可以将传统的根网站集转换为现代的根网站集，但现在有了这个版本，我们终于能够将现代的 SharePoint 网站作为根网站集，即使是在旧的 Office 365 租户上。

## 解

### 依赖关系

为了能够更新您的 SharePoint 主页，您首先需要下载并安装 SharePoint Online Management Shell 版本 16.0.8812.1200 或更高版本。要获取最新版本的 SharePoint Online Management Shell，请单击此处的[或运行以下 PowerShell 命令:](https://www.microsoft.com/en-in/download/details.aspx?id=35588)

安装模块 SharePoint pnppowershell online-force

### 工作原理

在下面的步骤中，我们假设如下:

*   http://yourtenant.sharepoint.com 的[有一个经典的根网站集](http://yourtenant.sharepoint.com)
*   你已经在你的租户那里创建了一个现代化的交流网站(例如在[http://yourtenant.sharepoint.com/sites/modernintranet](http://yourtenant.sharepoint.com/sites/modernintranet)

**重要说明:**Invoke-sposite swap cmdlet 不会将您现有的传统根网站集转换为现代网站集。相反，它用另一个现有的网站集(在本例中为现代通讯网站)替换当前的根网站集(类网站集)。

Invoke-SPOSiteSwap cmdlet 的语法如下:

invoke-spositeswap-source URL-target URL-archive URL[]

cmdlet 的主要参数是:

**来源 Url**

源网站的 URL。在执行交换之前，此位置的站点必须存在。

如果目标是位于[https://yourtenant.sharepoint.com](https://yourtenant.sharepoint.com)的根站点，那么源站点必须是团队站点(STS#0)、现代团队站点(STS#3)或通信站点(SITEPAGEPUBLISHING#0)。

在我们的例子中，这将是我们在[http://yourtenant.sharepoint.com/sites/modernintranet](http://yourtenant.sharepoint.com/sites/modernintranet)的现代通信站点，它代表了将取代我们旧的经典团队站点的站点。

**-目标 Url**

源站点将被交换到的目标站点的 URL。在执行交换之前，此位置的站点必须存在。

目标站点必须是:

*   根站点在[https://yourtenant.sharepoint.com](https://yourtenant.sharepoint.com)；或者
*   搜索中心位于[https://yourtenant.sharepoint.com/search](https://yourtenant.sharepoint.com/search)

在我们的示例中，这将是我们在 http://yourtenant.sharepoint.com 的经典团队站点根站点集合，它将被我们的现代通信站点所取代。

**-归档 Url**

目标网站将存档到的 URL。在执行交换之前，该位置应该没有现有站点，包括回收站中已删除的站点。

在我们的示例中，这将是我们的经典团队站点根站点集合将被归档到的 URL。

要查看全文，请点击[此处](https://blogit.create.pt/miguelisidoro/2019/08/27/how-to-modernize-your-tenant-root-site-collection-in-office-365-using-invoke-spositeswap/)。

分享快乐！

帖子[如何将 Office 365 中的经典根网站集转换为现代 SharePoint](https://blogit.create.pt/miguelisidoro/2019/08/27/how-to-modernize-your-tenant-root-site-collection-in-office-365-using-invoke-spositeswap/) 首先出现在[博客 IT](https://blogit.create.pt) 上。