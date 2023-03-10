# 使用 Azure web Jobs 和 PowerShell 删除旧的 Web 应用程序日志

> 原文：<https://dev.to/swimburger/deleting-old-web-app-logs-using-azure-web-jobs-and-powershell-37ml>

当你的 Azure 应用服务写入大量日志时，这些日志会很快堆积起来，甚至达到你的“文件系统存储”配额限制。这是我个人很长一段时间没有注意的事情，我惊讶地发现我的应用服务中有数千兆字节的日志。

要解决这个问题，你可以使用一个 PowerShell 脚本和一个时间触发的 Azure Web Job。

## 创建 PowerShell 脚本删除旧日志文件

Azure Web Jobs 支持多种语言:Windows cmd & exe、PowerShell、Bash、PHP、Python、NodeJS 和 Java。PowerShell 是一种非常好的脚本语言，可以在 Linux 和 Windows 上自动完成许多事情。您可以使用以下一行 PowerShell 脚本来删除超过 30 天的文件。

```
$LogFolder  =  "D:\home\site\wwwroot\App_Data\Logs";  $DaysToKeepLogsAround  =  30;  Get-ChildItem  -Path  $LogFolder  -Recurse  -File  |  Where  LastWriteTime  -lt  (Get-Date).AddDays(-$DaysToKeepLogsAround)  |  Remove-Item  -Force 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 Azure Web 作业以删除旧的日志文件

现在你的脚本已经准备好了，你可以使用以下步骤在你的 Azure 应用服务下创建一个 Azure Web 作业:

**1。导航到您的 Azure 应用服务下的 Web Jobs 部分，然后单击“添加”。**

[![Add Azure Web Job](img/40de6085eb3669a8e2cde92647be6dc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MoKUblaP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1149/cleanlogs-azure-web-job.png)

**2。填写添加 WebJob，如下图截图所示:**

[![Create Clean Logs Azure Web Job](img/612439afb37b13624494cb9265847252.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EPKABccU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1150/create-cleanlogs-azure-web-job.png)

WebJob 属性:

*   **Name** :为 WebJob 起一个不与应用服务中的其他 web job 冲突的名称。
*   **文件上传**:上传 PowerShell 脚本文件
*   **Type** :使用 triggered，这样您就可以设置一个 CRON 时间表，以便按时间间隔运行脚本。
*   **CRON 表达式**:输入一个有效的 CRON 表达式，指定执行脚本的时间间隔。Web 作业的 CRON 表达式使用的是开源项目 NCrontab。参考 GitHub 上的[NCrontab](https://github.com/atifaziz/NCrontab)来学习如何构建 CRON 表达式。你也可以在 ncrontab.swimburger.net 在线测试 NCrontab 的表达。 截图中的 WebJob 使用了“**0 0 * * ***”，相当于每天午夜运行脚本**。**

**3。点击 OK，你就一切就绪了。**

为了测试 WebJob，你可以从 Azure 门户界面点击“运行”。

**警告**:因为您正在使用脚本删除文件，所以在将脚本添加到您的生产环境之前，最好在安全的环境中测试一下。

这就是你如何设置一个 Azure WebJob 来使用 PowerShell 删除你的旧日志文件，干杯！