# 利用 AWS 系统管理器会话

> 原文：<https://dev.to/sheldonhull/leverage-aws-system-manager-sessions-l7j>

# 用例

利用 aws 系统管理器会话有助于 aws 开发，因为它消除了通过 PowerShell 会话完成工作的 RDP 需求。此外，它有助于绕过使用 SSH 隧道、远程 Windows 管理或 RDP 跳到达最终目的地的需要。

这利用了 IAM 凭证，允许与其他 IAM 策略保持一致的安全管理，而不是像远程管理那样必须管理另一个安全设置，从而潜在地减少了安全探索占用的空间。

*   快速访问通常需要额外一跳的实例，然后进行评估
*   重启远程服务，而无需直接进入(或发布 SSM 预建的命令文档)
*   以其他更为即兴的方式进行交互，并且没有现成的预建 SSM 文档。

## 浏览器

这是利用 AWS Systems Manager web 控制台的一个很好的选择。当您选择 start a session 时，您将看到按名称标记的实例，您可以快速选择并启动远程会话。

一旦你开始会话，你将进入一个远程提示。

## 本地交互设置

我将 Cmder 用于我的主终端，所有其他终端通常在 Visual Studio 代码中运行。如果您使用 powershell 插件打开 PowerShell 会话，您可以在编辑器中编写 Powershell，然后使用预定义的`F8`键在 Visual Studio 代码中交互式运行它。

### 在 Windows 上安装