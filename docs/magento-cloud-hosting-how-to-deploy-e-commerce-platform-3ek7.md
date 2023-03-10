# Magento 云托管。如何部署电子商务平台

> 原文：<https://dev.to/tetiana_ftv/magento-cloud-hosting-how-to-deploy-e-commerce-platform-3ek7>

网上购物行业在世界各地越来越受欢迎，因此电子商务网站在当今非常受欢迎。我们可以在网上找到许多选项来构建和托管电子商务网站，但 Magento 云托管被认为是市场上最强大、最灵活和可扩展的开源电子商务平台之一。

Magento 包括一系列工具来建立和管理一个在线商店。它在开发人员社区中以使用面向对象编程(OOP)而闻名，尤其是使用实体-属性-值(EAV)模型。这使得 Magento 在存储大量数据方面足够灵活。

Magento 可以在 Jelastic PaaS 的帮助下自动部署并托管在云中。拓扑中使用了两个不同网络服务器的安装包——NGINX 或 LiteSpeed。让它们运行起来非常容易，只需遵循下面几个简单的步骤。

1.进入 Jelastic 仪表盘，在[市场](https://docs.jelastic.com/marketplace)找到 **Magento 单机**包，点击**安装**。

[![](img/6fcaeb324b26a14ea73366c43535a661.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jJopTKTb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rkarj4m5b6238w9shdvl.png)
[![](img/41013e00653fd79e3380a5a5bddf9b75.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--6jm-2-IO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0b7o4bi7k0rkchc3vpx1.png)

或者，您可以[导入](https://docs.jelastic.com/environment-import)带有所需拓扑的 Magento [清单](https://github.com/jelastic-jps/magento/blob/master/magento/manifest.jps):

[![](img/2b63265716e4619c1ecebfd18c9f8841.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rRrkAv7H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ak3w1nw8lyrjua9qzfg6.png)

2.在弹出的对话框中，指定**环境**的名称，并选择首选的 **[区域](https://docs.jelastic.com/environment-regions)** 。

如果选择的服务提供商可以在 [LiteSpeed Web 服务器](https://docs.jelastic.com/litespeed-web-server)上安装 Magento，则会出现复选框“安装 LiteSpeed 高性能 Web 服务器”。如果你喜欢基于 [NGINX](https://docs.jelastic.com/nginx-php) 的安装，只要取消勾选这个选项。

点击**安装**按钮。

[![](img/04cb34b734edb05c508ba1a67d56786e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lyx1melA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0adseuf2rl99r6w2igvp.png)

3.等待几分钟，直到安装完成。

[![](img/20d5c5d2e295a0652c361e171eef17cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cS8cO2Xt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v3jhgtgq392avldldjem.png)

4.跟随 Magento 环境链接打开 Magento 欢迎页面。

[![](img/5239653d6aaa6cd35419d487317e9c68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wtc6Z96J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kvu3f9u8m41buq9nvu9m.png)

5.使用成功安装消息或收到的电子邮件通知中的凭据访问管理面板，并开始管理您的电子商务平台。

[![](img/a1806c182cdc79d37f1acdc591735fc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ibwj4yDl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ao6pqam586jrdmgjbnpv.png)

[![](img/6c612034ef02f3d15f2c69477caf8b1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3KNmgCC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o2e5u6bua6yopy51fcvv.png)

一切都准备好了！Magento 不仅用户友好，而且对商家有利，因为他们可以在一个管理面板中管理多个商店。这是一个完美的购物车，符合当今竞争激烈的业务要求。在公共的 [Jelastic PaaS 服务提供商](https://jelastic.cloud/?featuresSupport=MG)中亲自尝试一下。

我们希望这篇文章对你有用。如果你有可扩展 Magento 主机的经验，请在下面的评论区分享你的观点。