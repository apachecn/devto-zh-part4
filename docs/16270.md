# 需求，一个 PowerShell 等幂和声明性系统配置框架

> 原文：<https://dev.to/omiossec/requirements-idempotent-and-declarative-system-configuration-framework-in-powershell-3a40>

在 DevOps 世界中，关于作为代码的配置管理和基础设施，有两个重要的概念。

幂等性，一个操作可以应用多次但只在初次运行时才把状态改变到所需状态的性质。

声明性是一种编程范式，在这种范式中，你说什么是期望的状态，而不是(仅仅)如何去做。

我的一个朋友 FX [@lazywinadmin](https://twitter.com/lazywinadmin) ，给我指了一个新的框架，需求。这是一个用 PowerShell Core 构建的新配置管理框架。

需求用一组需求定义了一个状态。集合用于实现所需的状态。

需求可以是脚本或 DSC 资源(对于 PowerShell 5.x ),由以下部分组成:

*   名称，需求的名称。它在集合中必须是唯一的
*   描述，需求的描述，如创建文本文件或在 IIS 网站上更改矿/类型
*   DependsOn，一个数组，包含设置此需求之前所需的其他需求的名称

对于脚本需求，您还需要

*   测试，确定需求是否处于期望状态的测试。测试脚本需要返回一个布尔值，$true 或$false
*   Set，将需求置于期望状态的脚本

对于 DSC 要求，您需要

*   ResourceName，需求中使用的 DSC 资源的名称
*   ModuleName，资源使用的模块的名称。需求不管理模块，模块需要存在于机器上，但是你可以使用一个脚本需求来执行这个任务
*   属性，表示配置的散列表

需求看起来是这样的

```
@{  Name  =  "File-Add"  Describe  =  "Add test.txt file"  Test  =  {  test-path  -Path  "c:\test.txt"  }  Set  =  {  new-item  -Path  "c:\test.txt"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当你需要一个需求集合时，你需要使用一个数组

```
$requirements  =  @(  @{…}  ,  @{…}  ) 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用带有相同参数的 New-Requirement cmdlet 来创建一个需求对象

要开始配置，只需通过管道将需求数组对象传递给 invoke-Requirements

```
$MyRequirementsObjectList  |  invoke-requirements 
```

Enter fullscreen mode Exit fullscreen mode

也可以使用

```
Invoke-requirements  –Requirements  $MyRequirementsObjectList 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用 Test-Requirements cmdlet 来查看是否一切都处于所需的状态。

让我们看一个更深入的例子。第一个将创建一个文件并在当前目录中设置内容。这个脚本在 Linux 或 Windows 上与 Windows PowerShell 和 PowerShell Core 一起工作

```
$ErrorActionPreference  =  "Stop"  Import-Module  Requirements  $PathSeparator  =  [IO.Path]::DirectorySeparatorChar  $TextContent  =  "This is a script from Requirements"  $ScriptRequirements  =  @(  @{  Name  =  "File script"  Describe  =  "File script.txt is present in $PSScriptRoot"  Test  =  {  test-path  -Path  "$($PSScriptRoot)$($PathSeparator)script.txt"  }  Set  =  {  new-item  -Path  "$($PSScriptRoot)$($PathSeparator)script.txt"  |  Out-Null  }  },  @{  Name  =  "Set content in script"  Describe  =  "Ensure script.txt contain This is a script from Requirements"  Test  =  {  $fileContent  =  Get-Content  -Path  "$($PSScriptRoot)$($PathSeparator)script.txt"  $fileContent  -eq  $TextContent  }  Set  =  {  set-content  -Path  "$($PSScriptRoot)$($PathSeparator)script.txt"  -Value  $TextContent  }  DependsOn  =  @("File script")  }  )  $ScriptRequirements  |  Invoke-Requirement  |  Format-table 
```

Enter fullscreen mode Exit fullscreen mode

注意

```
$PathSeparator  =  [IO.Path]::DirectorySeparatorChar 
```

Enter fullscreen mode Exit fullscreen mode

我用它来处理 Linux 和 Windows 上的路径分隔符差异，这样我就不必关心在哪里运行脚本了。

第二个脚本做完全相同的事情，创建一个文件并设置其内容，但使用 DSC，因此它可以在 Windows PowerShell 上工作。

```
$ErrorActionPreference  =  "Stop"  Import-Module  Requirements  $file  =  "$($PSScriptRoot)\dsc.txt"  $TextContent  =  "This is DSC from Requirements"  $requirement  =  @{  Name  =  "Dsc"  Describe  =  "Dsc Requirement"  ResourceName  =  "File"  ModuleName  =  "PSDesiredStateConfiguration"  Property  =  @{  Contents  =  $TextContent  DestinationPath  =  $file  Force  =  $true  }  }  New-Requirement  @requirement  |  Invoke-Requirement 
```

Enter fullscreen mode Exit fullscreen mode

注意，我使用 New-Requirement 需求的第二种构造来创建对象。

需求模块适用于 PowerShell core(在 Linux 和 Windows 上)和 Windows PowerShell 5.1。该模块位于 PowerShell 图库中。

安装它

```
Install-module  –name  Requirement  –Scope  CurentUser 
```

Enter fullscreen mode Exit fullscreen mode

该项目位于 [GitHub](https://github.com/microsoft/Requirements) 上，目前仍在开发中