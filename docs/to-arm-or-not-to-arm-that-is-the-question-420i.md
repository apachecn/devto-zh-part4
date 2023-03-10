# 武装，还是不武装，这是一个问题

> 原文：<https://dev.to/janne_mattila/to-arm-or-not-to-arm-that-is-the-question-420i>

在过去的几年里，我已经被这个问题问了很多次了。
这都是关于当你使用 Azure 时如何选择你的基础设施即代码(IaC)解决方案。这些问题有不同的形状和形式，但其中许多可以总结为这个简单的问题:

"*我应该使用 **<占位符>** 来代替 ARM 模板吗？*

当然，你可以用不同的选项替换 **<占位符>** ，但最常提供的是基于 Terraform 和 Azure CLI 的解决方案。

人们通常期望我给他们一个简单的答案，比如“*做这个或做那个*”，但我不认为这是一个可以这样回答的问题。

让我们从您的开发团队中的现有经验开始。是否已经有人比其他人更了解这些选项中的一个？是一个人还是有更多有相似专长的人？您是否计划将单个应用程序的 IaC 资产重新用于不同的应用程序，或者您是否实际上从不同的供应商那里购买了这些不同的应用程序，他们总是提供他们的专业知识，而您不想或不能过多地参与其中？等等。

上述专业知识和经验相关的问题可能已经意味着你决定重用现有的知识，因为这使你能够更快地前进。实际上，我经常将这与软件开发中的编程语言选择进行比较:如果你很了解 Node.js，那么如果没有任何东西将你推向那个方向，你为什么要在下一个应用程序中使用 Python(当然，在这个例子中，你可以将这些编程语言与任何编程语言进行交换)。

如果你没有专业知识，或者你正在寻找评估这些不同的选项，那么我强烈建议你尝试 ARM 模板。在选择一个或另一个之前，有几个原因你应该知道。

让我们首先比较三个不同的简单部署(就像简单的资源一样),看看资源组在部署后是什么样的。

Azure CLI 方法([示例](https://docs.microsoft.com/en-us/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)):

[![Azure CLI](img/51fc62183f2137d251f39ce5d35834b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9K7VS0B4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3uavybvpfs3hrm46z6u8.png)

地形法([例](https://www.terraform.io/docs/providers/azurerm/r/application_insights.html)):

[![Terraform](img/a3ff286aa8ae37590c0735ae4deb82dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ke7FlU2l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bpkzbd7ppzdka96i7zou.png)

手臂模板法([例](https://gist.github.com/JanneMattila/2293534f4f8d5e2d0303bec8e353fc21)):

[![ARM template](img/fb3304c648d58db9f13b66a406621e3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--225hufRt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/osu8a4ui7kczrxc8kq3c.png)

好的，最终结果看起来是一样的。是吗？你能看出区别吗？如果您仔细查看 ARM 模板部署截图，您应该会注意到`Deployments: 1 Succeeded`(右上)。这是这些不同部署方法之间的重要区别。ARM 模板部署更新关于部署的资源组，因此它能够显示这些部署的历史和每个部署的细节。从[这里](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#template-deployment)阅读更多关于模板部署的信息。

这看起来可能是一件小事，但如果你将它与 Azure DevOps 的部署结合起来，你现在就可以轻松地将这两者映射在一起。

查看我们在 Azure Portal 中名为“app”的演示应用程序的“Dev”环境部署(您可以直接从资源组命名*service short name-environment-rg*):
[![Azure Portal deployments](img/0477f77e8f1a323fa825c48daaa36c3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hFlyXE4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fz55n3cjlvu3bte2bf1h.png)中导出“Dev”)

查看 Azure DevOps 端的“Dev”环境部署:
[![Azure DevOps Releases](img/c69453c6ae8f072dac97623236958cee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ReKysaq4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hz2mvnveq4jwm45mhdq8.png)

如你所见，它们是一对一的直接映射。

当 *PIIP* 遇到风扇时，这变得非常方便，你从 Azure 端对此进行故障诊断，并且你需要快速识别应用程序已经完成的实际部署。使用此解决方案，很容易看到生产环境的最后一次部署是在什么时候完成的:

[![Production deployment history](img/2a233effff926e97005db3aed6961f21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GM54mTld--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w4vthp38b8if835mdg3m.png)

这使您能够快速跳转到 Azure DevOps 端，并从源代码控制和工作项角度查看版本的内容以及以前和当前版本之间的差异。

* * *

如果您仍然决定使用基于 Azure CLI 脚本的部署，他们通常会遵循以下模式:

*   *添加资源 x*
*   *添加资源 y*
*   *添加资源 z*

等等。

你想过如果你决定摆脱资源 y T2 会发生什么吗？您必须手动删除它，因为脚本不会神奇地删除它(除非您有这样做的代码)。你甚至不应该有生产权限去做移除(甚至不要去想那个操作会有多可怕和危险！).Terraform 和 ARM 模板部署确实可以正确处理这种情况。只要记住在你的 ARM 模板部署中使用`-Mode Complete`用于[部署模式](https://docs.microsoft.com/en-us/azure/azure-resource-manager/deployment-modes) :

```
$result  =  New-AzResourceGroupDeployment  `
  -DeploymentName  $deploymentName  `
  -ResourceGroupName  $ResourceGroupName  `
  -TemplateFile  $Template  `
  @additionalParameters  `
  -Mode  Complete  -Force  `
  -Verbose 
```

这意味着不再属于模板的资源将自动从目标资源组中删除。

* * *

我经常听到的另一个评论是“ *ARM 模板是静态的*”以及“*在它们里面没有自定义逻辑的位置*”。我同意，我认为这是一件好事。我喜欢通过参数很好地控制我的 ARM 模板，但除此之外，它们只是普通的静态文件。但这也是我总是将 PowerShell `deploy.ps1`文件捆绑在它们旁边的原因(这里你可以将 PowerShell 替换成 Bash)。我认为这两者结合得很好:PowerShell 是部署的入口点，实际的基础设施资产在实际的 ARM 模板中描述。这也使得非常好的开发体验成为可能。假设您的团队中有了新的开发人员，他们希望将您的基础架构升级到他们的沙盒订阅。这就是他们要做的:

```
Login-AzAccount  Select-AzSubscription  -SubscriptionName  YourSubName  cd  C:\YourPathToApp\deploy  .\deploy.ps1 
```

这不是很简单吗？这很简单，因为`deploy.ps1`为部署提供了有意义的默认值。从[这里的](https://github.com/JanneMattila/118-FrontDoorApp/blob/master/deploy/deploy.ps1#L1-L21)可以看到这种设置的例子。

现在开发者不需要知道/记住如何正确设置`New-AzResourceGroupDeployment`的参数，因为所有这些都在`deploy.ps1`文件中处理。当然，每个环境中需要覆盖的所有参数都可以通过提供参数值来覆盖。

以上也意味着您可以在您的部署管道中使用相同的方法。所以不用这个:
[![Resource group deployment task](img/8547ee64812ee2bbf5251229d675a44e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z9k-cqnd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/86u4y3q8vc33utmqemhd.png) 
(截图因为太长所以被剪辑了！)

你可以直接用这个:
[![deployment using deploy.ps1](img/3f900ce8646b62163911a1215393940d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aESpkiSS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sb9e7a9w3i5hn8jrnl98.png)

* * *

我希望我能够证明，如果你以前没有尝试过，你应该旋转手臂。或者至少你现在应该知道我为什么喜欢 ARM 了。总结一下:

> 不要低估~~黑暗面~~手臂模板的威力！