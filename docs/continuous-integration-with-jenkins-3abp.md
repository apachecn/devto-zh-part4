# 与 Jenkins 的持续集成

> 原文：<https://dev.to/bala27/continuous-integration-with-jenkins-3abp>

软件开发生命周期的效率是区分成功的应用交付和失败的应用交付的关键。业界强调需要通过快速部署来提高开发过程的生产率。这可以通过持续集成和持续交付来实现。在敏捷开发中，经常需要通过构建的持续集成来定期测试代码。Jenkins 帮助优化 CI/CD 流程。让我们从持续集成开始，然后我们将继续讨论 Jenkins 在实现持续集成中的角色。

持续集成是一个每天进行多次提交的过程，并且每个提交代码都是立即构建和测试的。一旦测试通过，就要对构建进行部署测试，然后将它发送到生产环境。CI/CD 管道由持续集成服务器(如 Jenkins)、源代码控制工具、构建工具和自动化测试框架(如 Selenium、Appium 等)组成。持续集成有很多好处，根据项目需求，开发团队可以选择最好的持续集成工具。

什么是 Jenkins
Jenkins 是用 Java 编写的基于服务器的开源应用程序，用于以自动化方式实现持续集成。它之所以受欢迎，是因为它使得监控成为开发阶段出现的重复任务。在项目工作期间，Jenkins automation 将持续测试构建，并在开发的早期阶段识别错误。Jenkins 通过快速自动化构建和测试来加速软件开发过程。

**为什么 CI 需要 Jenkins**
当一个开发团队从事一个项目并使用共享存储库时，有些人可能需要 20 天，有些人可能需要 25 天才能完成。当开发人员完成他们的编码时，他们通常同时提交，然后测试构建。通过这种方式，一个单独的构建在许多天之后就完成了。但是使用 Jenkins，代码的构建和测试可以在一天内完成多次。一旦测试完成，源将被部署到测试服务器中。如果构建失败，Jenkins 会通知开发人员。

由于代码是一次完成的，一些开发人员不得不等待其他人完成，这样效率很低。很难检测和修复多次提交的错误。现在有了 Jenkins，一旦开发人员提交，代码就会立即构建，因此很容易找到导致构建失败的代码。

早期的代码构建和测试过程是手工完成的，这也是更多错误的原因。Jenkins 中的自动化构建和测试纠正了这一问题。

另一个问题是，当所有的错误都被修复时，代码就被部署了，这使得开发周期变得很慢。Jenkins 帮助加速这个过程，因为代码是在每次构建和测试之后部署的。

**优点和缺点**
Jenkins 最大的优点是它是开源的，社区从用户那里获取反馈来改进项目。每三个月就有一个稳定的版本，插件数据库中发布了超过 320 个插件。它还支持基于云的架构，并且可以与基于云的平台集成。

一些缺陷包括它过时的用户界面和在某些方面缺乏整体用户体验。它运行在服务器上，需要更多的维护和技能来安装、配置和监控活动。随着设置的改变，将会有一些问题，持续集成将会中断，这将需要开发人员的注意。

**使用 Jenkins 通过 pCloudy**
实现 CI/CD 管道在这里我们可以看到一个将在此过程中使用的示例应用程序代码。
[![](img/6cb0331513d3c063fbe57676e54ca2de.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--thZF2x8i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-1.png)

如图所示，这个应用程序也托管在 Git 上。
[![](img/712451a75f189dc6f9509268a21aaf14.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--u7Mpcqrj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-2.png)

转到 Jenkins 中该项目的配置页面。
[![](img/a814c9d11e5a41318767d90648587620.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--PUZRXSUd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-3.png)

我们已经将其配置为 pCloudySampleAndroidApp。Jenkins 将从该位置接这个项目并构建它。然后我们需要获得 Github 访问和分支来挑选代码。应该用一个时间表来轮询源代码控制机制。

在这次构建中，我们将使用 Gradle wrapper 构建 android studio 项目。

[![](img/6fd8ee3cd44f88d54264ead84ccb881d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3xaUEdRT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-4.png)

这里我们可以看到 Gradle 包装器的位置以及需要由 Gradle 执行的任务。

下一步是整合詹金斯和插件可以从 pCloudy 网站。
[![](img/bdc15fbb62279611d7f9da39c8a5293e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--QzPgOW7T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-5.png)

在步骤 1 中，我们提到了设备云的 URL，后面是用户名和 API 密钥。您可以通过进入 pcloudy 中的设置页面来检索 API 访问密钥。
[![](img/ef21ecee33e43755b41a0b6fbaec9dc4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--SlXm4ces--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-6.png)

在第 2 步中，我们需要提供命令行路径，在这里我们提到了包含测试用例的 Appium Jar 文件。接下来，你需要给出 Jenkins 构建的应用程序的路径，然后提到自动化持续时间。

在第 3 步中，我们可以选择要运行应用程序的设备。因此，您可以选择 Android 版本，并选择具有所需规格的首选设备。我们为此流程选择了两台设备。
[![](img/cb8fc8cf20704ce4e12b507d4f78f369.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--fHFCr13O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-7.png)

现在我们可以回到代码，检查整个配置是如何组合在一起的。
[![](img/72a99bebc9127521786f292a619dc906.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--lp8j47a9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-8.png)

在对代码进行更改之后，转到版本控制系统并添加一个提交。
[![](img/66e4a118dcb346dce09fea2edbdc59e3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--njO-mfEX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-9.png)

之后，按下代码。一旦推送成功，您就可以检查 Git 是否显示了代码更改。
[![](img/163c93dd7d7edb28a6a86a3a19d92cd7.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--M4n5Bzie--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/06/Jenkins-10.png)

现在，再次转到 Jenkins，单击“立即构建”。

在下一个屏幕中，我们可以看到 Gradle 守护程序的启动。

现在转到 device.pcloudy 中的活动会话，查看设备中活动的实时视图。

这就是你如何修改代码并将代码推送到 Git 的方法，Jenkins 从 Git 下载代码并将构建上传到 pCloudy。这个过程需要几分钟的时间，我们还能够实时看到测试活动，这是将基于 Jenkins 的构建系统与 pCloudy 的自动化相集成的一个优势。

**结论**
Jenkins 就像是持续集成流程的灵魂，因为它不断地构建和测试应用程序，这使得集成流程变更变得更加容易。大部分流程都是自动化的，这节省了时间和精力，可用于执行与交付相关的其他任务。它有一些像旧用户界面一样的问题，但是像一个活跃的开源社区这样的好处掩盖了一些小缺点。这就是为什么 Jenkins 是开发人员中最受欢迎的 CI 服务器。