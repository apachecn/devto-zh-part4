# 得益于 VS Code 和 Azure，Surface Go 的开发速度提高了 10 倍

> 原文：<https://dev.to/azure/developing-10x-faster-with-a-surface-go-thanks-to-vs-code-and-azure-mho>

作为开发人员，我们总是寻求最高效的环境，这通常意味着购买高端笔记本电脑。我们将在这篇文章中看到，使用 Azure 进行 VS 代码远程开发可以以更低的成本提供更好的环境。

我相信这对咨询公司和自由职业者来说是一场革命，这可能会改变我们未来的工作方式。

不过先说个故事吧！

## Surface Go，开发者的牛逼机器？

我最近给自己买了一台 [Surface Go](https://www.microsoft.com/en-us/p/surface-go/8v9dp4lnknsz?source=lp&activetab=pivot:overviewtab&WT.mc_id=devto-blog-judubois) 供自己使用。这是我在微软工作之前的事，事实上与我的工作活动完全无关。我的理由是:

*   它非常轻，非常小
*   它非常便宜，在亚马逊上总是有折扣，你不到 500 美元就能买到
*   它有 Windows 10 及其应用商店，包括网飞(任何有孩子的人都必须拥有，因为我们都知道那些离线“爪子巡逻”剧集在旅行时是多么有用)
*   它有一个 UBS-C 端口，所以我只需将其插入我的工作显示器(Dell UltraSharp 27 - U2719DC ),一切都可以开箱即用

**非常重要的说明:**Surface Go 有 2 个版本，用高端的。你不仅有两倍的内存和磁盘，而且你有一个真正的固态硬盘快得多。

当然，作为一名开发人员，我也用它来编码，所以问题是:这对编码有多好？

## 我的发展设置

我用棒极了的 [Chocolatey](https://chocolatey.org/) 包管理器设置了所有东西，包括:

*   [AdoptOpenJDK](https://adoptopenjdk.net) JDK 发行。
*   [NodeJS](https://nodejs.org) 。
*   [Cmder](https://cmder.net/) 终端仿真器。
*   [Visual Studio Code Insiders](https://code.visualstudio.com/insiders/?WT.mc_id=devto-blog-judubois) ，它提供 VS Studio 代码的每日构建，并且是在撰写本文时**访问 Visual Studio 代码远程扩展的途径**。

## 初始绩效数字

作为 JHipster 项目的负责人，我当然会对 JHipster 应用程序进行这些性能测试，但这里并没有什么真正针对 JHipster 的，所以它们应该对任何 Java 进程都是一样的，甚至对任何使用大量 CPU、RAM 和磁盘的进程都是一样的。

我在[https://github.com/jdubois/simple-jhipster-app](https://github.com/jdubois/simple-jhipster-app)创建了一个简单的项目，你可以很容易地在你自己的机器上测试它:

*   克隆项目:`git clone git@github.com:jdubois/simple-jhipster-app.git`
*   运行 Maven 包装:`./mvnw`

从性能的角度来看，这看起来不太好:

*   Maven 需要很长时间来运行
*   Spring Boot 在 13.5 秒后开始

让我们看看如何改进这一点！

## 表面围棋 vs .表面书

谢天谢地，为了工作，我有一台中档(core i5) Surface Book，所以用那个硬件应该更好，不是吗？

我在这台机器上安装了相同的设置:启动 Spring Boot 应用程序现在需要 12 秒，而不是 13.5 秒！我们所做的有点依赖于 CPU，但我们的大多数问题可能都与 I/O 有关，两个硬件都有相同的内存，可能还有相似的 SSD。

所以，从成本角度来看，第一个有趣的教训是:在这里购买 500 美元的 Surface Go 是一笔非常划算的交易，因为它的价格是 Surface Book 的四分之一，也是你背包重量的四分之一！

## 使用 VS 代码并删除 Maven

让我们使用 [Visual Studio Code Insiders](https://code.visualstudio.com/insiders/?WT.mc_id=devto-blog-judubois) 打开应用程序，当然还要安装来自微软的[“Java 扩展包”。](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack&WT.mc_id=devto-blog-judubois)

由于 Spring Boot 应用程序也是普通的“主”Java 类，所以可以通过运行`SimpleApp`类中的`main`方法来运行应用程序。这比运行 Maven 容易得多，但也快得多。而且还可以一键调试应用！

我们现在获得了 Maven 的启动时间，但是应用程序仍然需要 13.5 秒才能启动。

## 使用 VS Studio 代码远程开发

再次打开 [Visual Studio Code Insiders](https://code.visualstudio.com/insiders/?WT.mc_id=devto-blog-judubois) ，安装微软的[“远程开发”扩展。](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack&WT.mc_id=devto-blog-judubois)

这将允许我们在 SSH 机器上工作，SSH 机器将由 Azure 提供。

让我们在 Azure 上创建虚拟机:

*   前往 [Azure 门户](https://portal.azure.com/?WT.mc_id=devto-blog-judubois)。
*   创建新的虚拟机。
*   在撰写本文时，选择默认的机器类型 D2Sv3。这需要每小时 0.1120 美元。
*   在第一个屏幕上添加您的公钥，在网络屏幕中，不要忘记打开`Select inbound ports`下拉列表中的 SSH 端口。
*   默认情况下，保留所有其他选项，并创建机器。

一旦创建了机器，让我们用我们通常的工作环境来配置它。我个人有自己的安装脚本，您当然可以重用它:

*   该脚本可从[https://github . com/jdubois/jdubois-configuration/blob/master/install . sh](https://github.com/jdubois/jdubois-configuration/blob/master/install.sh)获得
*   使用 SSH 登录到您的新 Azure 虚拟机:`ssh username@machine-ip`(请注意，您可以在 Azure 门户网站的“概述”页面上找到您机器的 IP 地址)，并成为 root : `sudo su`
*   登录后，以 root 用户身份运行我的安装脚本:`sh -c "$(curl -fsSL https://raw.githubusercontent.com/jdubois/jdubois-configuration/master/install.sh)"`

恭喜你，你的 Azure 机准备好了！

让我们回到 VS 代码并连接到它。因为我们会经常使用这个 SSH 连接，所以我建议您在您的主文件夹中创建一个`.ssh/config`文件，其设置类似于这个:

```
Host jdubois-azure
    HostName 52.232.60.209
    User julien 
```

一旦您连接到 Azure 虚拟机:

*   使用扩展管理选项卡再次安装来自微软的[“Java 扩展包”，但是这次是在远程 SSH 主机上。](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack&WT.mc_id=devto-blog-judubois)
*   使用命令面板对我们的示例应用程序`https://github.com/jdubois/simple-jhipster-app.git`进行 Git 克隆。
*   像我们之前做的那样运行`SimpleApp`类。

因此，我们的应用程序现在只需 10.3 秒就能启动！这比我们的 Surface Go 要好一点，但显然默认虚拟机不是我们的最佳选择。

**请注意**如果您想在 [http://localhost:8080](http://localhost:8080) 上本地访问您的应用程序,“远程开发”选项卡为您提供了一个创建 SSH 隧道的图形实用程序。

## 用更好的机器测试，优化价格

由于我们使用虚拟机，我们可以做一些非常有趣的事情来优化成本和性能:

*   那些机器应该只在你使用的时候才运行，所以你应该在工作的时候打开和关闭它们，事实上就像一台普通的电脑一样！请注意，在 Azure 门户网站上有一个“自动关机”菜单，在晚上或周末自动关闭机器可能是一个好主意，以防你忘记这样做。
*   有一个“大小”菜单，允许您更改正在使用的机器类型，以便根据您想要的成本和性能进行调整。不幸的是，这并不适用于所有的机器:它们需要在同一个地区，在同一个家族中。

让我们再次运行我们的测试，看看我们的应用程序可以获得的最佳性能，并计算其每日相关成本(我们在这里考虑您每天工作 10 小时，每年 240 天)。以下是结果以及相关成本(针对“西欧”地区)，其中机器看起来最有前途:

| 机器类型 | 应用程序启动时间 | 每日成本(10 小时) | 年度成本(240 天) |
| --- | --- | --- | --- |
| D2S v3(默认) | 10.3 秒 | One point two | Two hundred and eighty-eight |
| F2s v2 | 5.8 秒 | Zero point nine seven | Two hundred and thirty-three |
| F4s v2 | 4.0 秒 | One point nine four | Four hundred and sixty-six |
| F8s v2 | 3.4 秒 | Three point eight eight | Nine hundred and thirty-one |
| H8 | 2.9 秒 | Ten point three two | Two thousand four hundred and sixty-seven |
| H8 促销 | 2.9 秒 | Six point two | One thousand four hundred and eighty-eight |

让我们研究一下这些数字:

*   默认的机器(D2S v2)不是很有趣:它比 Surface Go 快 30%，所以它真的不值得这个价格和麻烦。
*   “F”机器，即 Azure 上的“计算优化”机器。从“F4s”开始，它们还可以访问高级 SSD 和加速网络，因此升级时您获得的不仅仅是 CPU 能力。
*   “H”机器是为了“高性能计算”，所以基本上它们比“F”机器有更好的 CPU。然而，它们要贵得多，而且正如我们前面看到的，我们对 CPU 的依赖性不是很强。目前它们中的一些有“促销”，这使得它们的价格更有吸引力，在这种情况下，它们可以成为“F”机器的有趣替代品。

因此，看起来“F”型机器确实是最划算的。我个人会使用 F4s v2 或 F8s v2，并使用“调整大小”功能根据我的需要进行切换。因为我订阅了 MSDN，我每个月有 130 美元的 Azure 信用，所以甚至 H8 的推广也可以工作！

## 结论一个想法

显然，以合理的价格，在“F”机器类型上运行我们的应用程序要比在 Surface Go 上快得多。此外，我们能够“调整”和关闭这些机器，以适应我们所需的成本和性能。

对于咨询公司和自由职业者来说，我相信这里有一个非常有吸引力的选择，可以替代目前每个人都购买的 2500 美元的 Macbook Pro:

*   前期费用非常便宜(约 500 美元)，所以买一台不像买 MacBook Pro 那么重要。
*   这是我们对云进行的常见的资本支出与 OPEX 计算:作为一名顾问，我宁愿在创业时不花这 2500 美元，而是每天花几美元，这取决于我的收费工作。
*   我相信这些每天几美元的费用可以向客户收取:这并不需要花费太多(与顾问花费的时间相比！)，为人们赢得了登机时间，并提供了更好的安全性(代码不会复制到每个人的笔记本电脑上)。