# 需要将 Webapp 从 Azure 迁移到 NAS 的帮助

> 原文：<https://dev.to/saigkill/need-help-by-moving-a-webapp-from-azure-to-nas-4im5>

开发者们好，
我用 ASP 创建了一个网站。NET Core 及其源代码放在那里:[https://github.com/saigkill/saschamannsde](https://github.com/saigkill/saschamannsde)

8 个月来，它一直作为 web 服务在 Azure 中运行。现在，我想将该 Webapp 移动到我的 NAS。我在那里准备了一个基于 Windows Server 2019 Essentials 的虚拟系统，并在那里放置了托管包，这取决于我的。NET 核心版。对于我尝试的最后一次提交，通过配置 Kestrel 为在我的 NAS 中使用做准备。

我还在目标系统上准备了 IIS 和 WebDeploy。构建和部署过程按预期工作。但是一个 [http://localhost:5000/](http://localhost:5000/) 没有显示任何部署。

也许有人已经完成了这样的任务，可以看看(也许通过 TeamViewer)？

你好，沙夏