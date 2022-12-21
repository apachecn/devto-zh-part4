# AWS SSM PowerShell 脚本自动化

> 原文：<https://dev.to/sheldonhull/aws-ssm-powershell-script-automation-1cg>

## SSM 概述

我发现在 AWS 中处理大量的环境会给执行各种任务带来一些有趣的挑战。

当您开始拥有数十到数百台可能需要快速修复的服务器时，您最不想做的事情就是 RDP 到每台服务器并执行某种类型的脚本操作。

AWS SSM(系统管理器)提供了大量的功能来帮助管理系统。从运行脚本、安装应用程序和其他面向日常管理的任务，到更复杂的状态管理、AMI 自动化和其他可能超出虚拟机管理范围的任务，It 人员都可以执行。

在接下来的几篇文章中，我可能会解开其中一些领域，因为在过去的几个月中，我的世界一直非常关注 SSM 的使用，并且利用它对于那些大量使用 EC2 的人来说是必须的。

## PowerShell 执行

对于第一个简单的例子，AWS SSM 提供了包装各种脚本动作和接受参数的文档。这可以是加入一个域或运行一个 shell 脚本。

在我的例子中，我需要更改注册表设置，重新启动 windows 服务，或者在整个环境中设置环境变量。另外，我想将这次运行的目标设置为标签过滤器，而不是提供 instanceid，因为这个环境经常作为开发的一部分被重建。

执行脚本的命令有一个我讨厌的缺陷。我讨厌转义字符串。
这可能来自于我对掌握动态 t-sql 的专注努力💩在这一点上，我很快试图尽可能避免使用动态 sql，因为我意识到它并不是我刚开始学习时所认为的最终解决方案。

有了 PowerShell 和 AWS SSM，事情会变得更糟。您必须传入命令，并希望所有的 json 语法和转义不会出错。

## 解

按照本机设计编写 PowerShell，然后对这个 scriptblock 进行编码，以便作为编码的命令进行传递。我发现，对于我的大部分临时工作来说，这提供了一个完美的解决方案，消除了我对必须转义代码的任何担忧，同时还允许我在我的 Vscode 编辑器中编写带有完整林挺和语法检查的本机 PowerShell。

### 认证

```
Import-Module AWSPowershell.NetCore, PSFramework #PSFramework is used for better config and logging. I include with any work i do
$ProfileName = 'taco'
$region = 'us-west-1'
Initialize-AWSDefaultConfiguration -ProfileName $ProfileName -region $region 
```

Enter fullscreen mode Exit fullscreen mode

### 创建你的命令

在本节中，我提供了一种引用现有函数的方法，这样远程实例就可以在本地脚本执行中包含该函数，而不必直接将其复制并粘贴到您的命令块中。为胜利而干。

```
#----------------------------------------------------------------------------#
# Include this function in remote command #
#----------------------------------------------------------------------------#
$FunctionGetAWSTags = Get-Content -Path 'C:\temp\Get-AWSTags.ps1' -Raw
$command = {
Get-Service 'codedeployagent' | Restart-Service -Verbose
} 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经有了一个脚本块，可以开始编码了。这种编码将使你不必担心转义引号，而且你可以在普通编辑器中编写整个脚本，而不会在林挺遇到任何问题。

```
#----------------------------------------------------------------------------#
# encode command to avoid escape issues #
#----------------------------------------------------------------------------#
[string]$CommandString = [string]::Concat($FunctionGetAWSTags, "`n`n", $Command.ToString())
$bytes = [System.Text.Encoding]::Unicode.GetBytes($CommandString)
$encodedCommand = [Convert]::ToBase64String($bytes)
$decodedCommand = [System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String($encodedCommand));
Write-PSFMessage -Level Debug -Message "Decoded Command: $($DecodedCommand)" 
```

Enter fullscreen mode Exit fullscreen mode

在我的本地脚本中，我还将包括这个 Wait-SSM 命令，这是一种快速等待 SSM 作业的结果完成并显示状态的方法。
这是因为 Send-SSMCommand 实际上是一个异步命令，它不等待完成，只等待命令的成功发送。

```
function Wait-SSM
{
param(
[Amazon.SimpleSystemsManagement.Model.Command]$Result
)
end
{
$Status = (Get-SSMCommandInvocation -CommandId $Result.CommandId -Details $true | Select-Object -ExpandProperty CommandPlugins).Status.Value
while ($status -ne 'Success')
{
$Status = (Get-SSMCommandInvocation -CommandId $Result.CommandId -Details $true | Select-Object -ExpandProperty CommandPlugins).Status.Value
Start-Sleep -Seconds 5
}
Get-SSMCommandInvocation -CommandId $Result.CommandId -Details $true | Select-Object InstanceId, Status | Format-Table -Autosize -Wrap
}
} 
```

Enter fullscreen mode Exit fullscreen mode

### 发送命令

最后，我们吃肉🍗还有土豆……或者对我来说，我更喜欢肉和玉米卷🌮的问题。

发送命令…

```
 $Message = (Read-Host "Enter reason")
$sendSSMCommandSplat = @{
Comment = $Message
DocumentName = 'AWS-RunPowerShellScript'
#InstanceIds = $InstanceIds # 50 max limit
Target = @{Key="tag:env";Values=@("tacoland")}
Parameter = @{'commands' = "powershell.exe -nologo -noprofile -encodedcommand $encodedCommand"
}
}
$result = Send-SSMCommand @sendSSMCommandSplat
Wait-SSM -Result $result 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您还可以传入一个实例列表。为了做到这一点，我建议首先基于标签过滤，然后也过滤到可用于 ssm 来运行命令，以避免在不会成功的实例上运行，例如关闭的实例，或者没有运行 SSM 的实例。

## EC2 过滤器

为了简化标签的使用，我经常使用 David Christian (@dchristian3188)编写的`ConvertTo-Ec2Filter`函数，可以在这篇博文中看到 [EC2 标签和过滤](http://bit.ly/2KYcWGF)。

```
Function ConvertTo-EC2Filter
{
[CmdletBinding()]
Param(
[Parameter(
ValueFromPipeline,
ValueFromPipelineByPropertyName)]
[HashTable]
$Filter
)
Begin
{
$ec2Filter = @()
}
Process
{
$ec2Filter = Foreach ($key in $Filter.Keys)
{
@{
name = $key
values = $Filter[$key]
}
}
}
End
{
$ec2Filter
}
}

$searchFor = @{
'tag:toppings' = 'saucesAndMoreSauces'
'tag:env' = 'tacoland'
}
$ssmInstanceinfo = Get-SSMInstanceInformation
$ec2Filter = ConvertTo-EC2Filter -Filter $searchFor
$Instances = @(Get-EC2Instance -Filter $ec2Filter).Instances
[string[]]$InstanceIds = ($Instances | Where-Object { $_.State.Name -eq 'running' -and $_.InstanceId -in $ssmInstanceinfo.InstanceId } | Select-Object InstanceId -Unique).InstanceId 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

希望这将帮助您使用 Send-SSMCommand，以一种简单的方式跨任意数量的 EC2 实例发出命令。对我来说，在标记的环境中运行命令节省了大量的手动控制台工作，允许我更快地应用修复或 chocolatey 包，或者测试环境中的任何数量的需求，而没有每个实例的所有开销，或者使用可怕的 RDP💩联系。

如果你发现一些不清楚或者值得更多解释的地方，我随时准备编辑和重构这篇文章。🎉