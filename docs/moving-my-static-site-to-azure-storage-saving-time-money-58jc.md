# 将我的静态站点迁移到 Azure 存储，节省时间和金钱

> 原文：<https://dev.to/halldorstefans/moving-my-static-site-to-azure-storage-saving-time-money-58jc>

*最初发表于 2020 年 1 月 13 日[halldorstefans.com](https://www.halldorstefans.com/moving-my-static-site-to-azure-storage-saving-time-money/)我的博客上*

大约五个月前，大约在时间*。dev* 域可用，我想尝试在服务器上建立一个简单的网站。有两个原因。拥有一辆*。dev* 域并创建一个小型 Linux 项目。最近，我在 Azure 中积累了一些知识，因此，我想知道 Azure 中有哪些选项可以用来托管我的站点。

当*。dev* domains 变得可用，我马上知道我想要一个(仅仅因为:)，并决定用它创建一个 web 服务器项目。

我当时选择了数字海洋。原因是我发现他们有很棒的教程，看起来比 Azure/AWS 更简单，也不那么吓人。我用 *[这个教程](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)* 作为我的起点。最终，一切都很顺利，这是一个有趣的小项目。

然而，由于我对 Azure 的兴趣在过去几个月里一直在增长，我开始思考 Azure 是否有任何替代方案可以让我不需要自己维护服务器。

在我看了伯克荷兰之后的*这是如何轻松部署一个静态站点到 Azure* 和微软 *[教程](https://docs.microsoft.com/en-gb/azure/storage/blobs/storage-blob-static-website-host)* 之后，我发现 Azure 并没有我一开始想象的那么可怕。一旦我意识到没有回头路可走，我必须尝试一下。

[![Microsoft Azure](img/a44140291734a0e75ccb2fdfa9feb97a.png)![](img/48a31caeb237a0669d58aae0555350e2.png)](/azure) [## 这就是如何轻松地将静态站点部署到 Azure

### Burke Holland for Microsoft Azure 1910 年 5 月 30 日阅读

#node #javascript #webdev #programming](/azure/this-is-how-to-easily-deploy-a-static-site-to-azure-31on)

由于 Burke Holland 和微软在这方面做得很好，我不打算在 Azure 中运行这个网站，如果你想尝试一下，我推荐这些说明。

我已经在 Azure storage 上运行了两周，成本仍然是 0 美元。在这两个星期里，我一直把这滴保存在 DigitalOcean 中，以防万一会出现什么问题，或者我想回到它身边。但是这段时间的费用在 2 美元左右(我用的是最便宜的滴滴，5 美元/月)。因为我对结果很满意，我的这个小项目也很成功，所以我决定删除我的 Droplet，暂时使用这个解决方案。

虽然 5 美元/月不多，但想办法省钱总是好的。另外，我还跳过了维护服务器。所以，我节省了时间和金钱。双赢。

* * *

感谢您的阅读。对于我的每周更新，你可以注册我的[时事通讯。](https://email.halldorstefans.com/)