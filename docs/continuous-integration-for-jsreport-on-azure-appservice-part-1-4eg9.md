# Azure AppService 上 JSReport 的持续集成-第 1 部分😉😉

> 原文：<https://dev.to/irajbalakrish/continuous-integration-for-jsreport-on-azure-appservice-part-1-4eg9>

最近，我挑选了一个用户故事来为. NET 核心应用程序构建 PDF 报告。在尝试了 PDFSharp 之后，我放弃了，因为要让它工作需要大量的管道工程。NET Core 在 C#中绘制整个 PDF 设计的顶部

我开始寻找一个替代方案，并偶然发现了 jsreport，这是一个整洁优雅的库/服务，用于使用 HTML 和 handlebars 模板绘制 PDF 报告。

你可以从各种算法中选择，使用 PhantomPDF、Electron、Chromium、WkhtmltoPDF 等引擎生成 PDF..该服务以 SaaS 模式提供，有可能使用 MSFT Azure、AWS 等各种技术栈在本地托管它..，当然还有一个客户端 SDK。

使用这项服务的最大好处是，他们有一个活跃的社区，我认为这项服务的开发者 Jan Blaha 可以回答问题，以极快的速度提供热修复:)你有问题。，那么修复程序的周转时间不到一天或两天！！

我尝试在 jsreport Studio 中创建我的 PDF 模板，连接到 DotNetCore WebApi，最后让 Angular 应用程序使用它。这非常有效。

现在，我想将这个服务用于团队中的其他应用程序。因此，这需要在多个开发测试和生产环境中自行托管+托管到添加新 PDF 模板的整个过程需要自动化

选择的平台是 Azure，提取图像的方法是通过 Docker Hub 公共注册中心。这包括来回导航门户刀片以创建用于身份验证的 AppServicePlan、AppService、容器配置、StorageAccount 和 AppSetting 配置以及用于安全性和模板持久性的连接字符串

# 简而言之，一项服务需要大量的点击..

因此，第一步是使用 ARM 模板自动将 jsreport 服务部署到 Azure。快速编写了一个 ARM 模板，可以创建一个 Linux Service + StorageAccount，从 Docker Registry 中提取 js reports 2 . 1 . 0-完整版构建，并配置身份验证和嵌入连接到存储帐户所需的所有设置，以便持久化模板。

# 简而言之，ARM Templates 只需点击几下鼠标，就能构建出你自己的 Azure jsreport 服务环境

我将很快把这个模板提交给 [Azure QuickStart](https://azure.microsoft.com/en-us/resources/templates) ARM 模板集合。在那之前，你可以从我的 [Github 库](https://github.com/irajbalakrish/JSReportsAzureDeployment)访问这个模板，并部署到你的 Azure 环境中，让你的 jsreport 自托管版本在几分钟内启动并运行

要了解如何使用 Visual Studio 部署 ARM 模板，[请阅读本文](https://docs.microsoft.com/en-us/azure/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy)

在下一部分中，我将讲述如何使用 VSTS 和方法部署这个 ARM 模板，从而以完全自动化的方式将在 studio 中创建的 jsreport PDF 模板发布到其他 jsreport azure 环境中