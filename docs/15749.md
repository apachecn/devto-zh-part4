# PowerShell 中的单元测试

> 原文：<https://dev.to/omiossec/unit-testing-in-powershell-introduction-to-pester-1de7>

当人们(至少是 IT 人员)想到 PowerShell 时，他们会想到一些神奇的命令，可能还会想到一些脚本，有时甚至更多。
PowerShell 不止这些。它不仅是一种顺序编程语言，还是一种函数式语言。你可以创建函数和模块，但你不仅可以使用它作为 DSC 和其他工具的配置管理，你还可以在 AWS Lambda 或 Azure Functions 等无服务器平台上使用它，你可以在 Azure 或 AWS 上构建云解决方案。您可以使用 PowerShell 构建完整的解决方案。

即使 PowerShell 被视为 Ops 工具，它也遵循与任何其他编程语言相同的方法和模式。单元测试就是这些模式中的一种。它确保逐节代码按预期工作。

PowerShell 拥有一个单元测试框架。它的名字叫[perste](https://github.com/pester/Pester)，是 PowerShell 无处不在的测试和模拟框架。它是一种领域定义语言，也是一套运行单元和验收测试的工具。

## 安装纠缠

即使现在 Windows 10 上默认安装了 Pester，最好还是更新到最新版本(现在 4.8.x，很快 5.x)。
Pester 可以安装在 Windows PowerShell(即使是旧版本)和 PowerShell Core 上

```
Install-module  -name  Pester 
```

Enter fullscreen mode Exit fullscreen mode

如果已经安装了另一个参数
，您可能需要使用 force 参数

```
Install-module  -name  Pester  -force 
```

Enter fullscreen mode Exit fullscreen mode

是的，你不需要再使用-SkipPublisherChek 了，现在已经签名了。

## 基本

测试脚本以 Describe 开始。描述块创建测试容器，您可以在其中放置数据和脚本来执行您的测试。在 describe 块中创建的每个变量都会在该块执行结束时被删除。

```
Describe  {  # Test Code here  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以为每个描述块添加标签和名称来定义测试场景

```
Describe  -tag  "SQL"  -name  "test1"  {  # Test Code here  }  Describe  -tag  "SQL"  -name  "test2"  {  # Test Code here  } 
```

Enter fullscreen mode Exit fullscreen mode

describe 块中的测试可以分组到一个上下文中。
上下文也是一个容器，在上下文块中创建的每个数据或变量在上下文块执行结束时被删除
上下文和描述定义了一个范围。

```
Describe  -tag  "SQL"  -name  "Sqk2017"  {  # Scope Describe  Context  "Context 1"  {  # Test code Here  # Scope describe 1  }  Context  "Context 2"  {  # Test code Here  # Scope describe 2  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## It Block

要创建一个带有 percept 的测试，我们只需使用关键字 It。It 块包含测试脚本。这个脚本应该抛出一个异常。It 块需要有一个明确的描述它“返回某物的名称”和一个脚本块。描述在范围内必须是唯一的(描述或上下文)。
It 描述可以是静态的，也可以是动态创建的(即:只要描述是唯一的，就可以使用变量作为描述)

该命令应该允许您将对象、代码中的某些内容与预期的内容进行比较，并在 It 块失败时抛出一个错误。应该引入一个断言，这个断言必须为真，否则抛出一个错误。

有几种说法:

| 断言 | 描述 |
| --- | --- |
| 存在 | 比较这两个对象 |
| 准确地说 | 在区分大小写模式下比较两个对象 |
| 贝加尔特汉 | 对象必须大于值 |
| begreaterrorequal | 对象必须大于或等于值 |
| 嬉痞的集会 | 测试对象是否在数组中 |
| 不到 | 对象必须小于值 |
| 贝莱索勒夸尔 | 对象必须小于或等于值 |
| 或许 | 执行 a -li 比较 |
| 确切地说 | 执行区分大小写-li 比较 |
| BeOfType | 像-is 运算符一样测试值的类型 |
| 受影响 | 检查该值是否为真 |
| 虚伪 | 检查该值是否为假 |
| 有计数 | 数组/集合必须具有指定的数量值 |
| 包含 | 数组/集合必须包含值，就像-contains 运算符一样 |
| 存在 | 测试对象是否存在于 psprovider(文件，注册表，...) |
| 文件内容匹配 | 文本文件中的正则表达式比较 |
| 文件内容完全匹配 | 文本文件中区分大小写的正则表达式比较 |
| 文件内容匹配多行 | 多行文本文件中的正则表达式比较 |
| 比赛 | RegEx 比较 |
| 完全匹配 | 区分大小写的正则表达式比较 |
| 扔 | 检查脚本块是否抛出错误 |
| BeNullOrEmpty | 检查值是否为 null 或空字符串 |

```
Describe  "test"  {  It  "true is not false"  {  $true  |  Should  -Be  $true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，It 模块分为两部分。第一个，名称，标识测试。第二个，脚本块可以包含您需要的任何代码，只要您通过管道将结果传递给一个 Should 语句。如果语句的条件不满足，该语句将评估数据并抛出一个错误。
我们也可以用-not 关键字
构建否定断言

```
Describe  "test"  {  It  "true is never false"  {  $true  |  Should  -not  -Be  $false  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当我编写单元测试时，我经常需要用不同的条件或不同的数据测试相同部分的代码或功能几次。由于我很懒，我不喜欢写同一个测试超过一次或者两次。为此，我只需要一个字典对象和测试用例

```
$BufferSize  =  4  $DataTypeName  =  [System.Byte[]]::new($BufferSize)  $RandomSeed  =  [System.Random]::new()  $RandomSeed.NextBytes($DataTypeName)  $TestData  =  @(@{"TestValue"  =  "Value One";  "TestType"  =  "String"},@{"TestValue"  =  2;  "TestType"  =  "int32"},@{"TestValue"  =  $DataTypeName;  "TestType"  =  "Byte[]"})  function  get-DataTypeName  {  [CmdletBinding()]  param  (  $value  )  return  ($value.getType()).name  }  Describe  "test some values"  {  It  "Test if <TestValue> is a <TestType> Object"  -TestCase  $TestData  {  param($TestValue,  $TestType)  get-DataTypeName  -value  $TestValue  |  Should  -Be  $TestType  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当您对 dictionary 对象使用-TestCase 时，It 块将迭代该对象，并为每次迭代执行一次测试。
要显示 It 描述中的值，需要< >。It 集团内部；您需要将数据作为参数传递，并在参数变量中获取它们的值

## 关于如何编写和运行纠缠测试

您几乎可以在任何 PowerShell 文件中运行 pester，但是按照惯例，建议在您的解决方案中使用名为 xxx.tests.ps1 的文件，每个 PowerShell 文件一个。如果你的每个函数都有一个文件，那就意味着只有一个 pester .tests.ps1\.
你可以将 pester 文件放在与你的源代码相同的文件夹中，或者你可以使用库根目录下的 tests 文件夹。就个人而言，我更喜欢使用测试文件夹，但是无论测试文件在哪里，它们都需要加载代码。

要做到这一点，你可以点源你需要测试的脚本。

要运行. tests.ps1 文件中的所有测试，您只需要使用 tests 文件夹中的 invoke-pester。如果 It 和 Describe 模块是测试过程的核心，那么 invoke-pester 就是肝脏，因为它可以过滤测试并转换结果。

通过将-Testname 或/和-Tag 参数与-passThru 一起使用，您可以使用测试名称或标记来过滤 describe 块。

-Strict 参数允许考虑任何挂起或跳过的测试，这将导致失败。

默认情况下，pester 在控制台中直接以文本形式产生结果。在某些情况下，您可能希望使用另一种格式。

-PassThru 参数允许您生成自定义 PowerShell 对象，而不是标准输出。它生成一个 PSCustomObject，其中包含测试次数、通过次数、跳过次数、挂起次数或失败次数。它还包含一个数组，TestResult 包含所有的测试结果。

-EnableExist 开关将使用退出代码使 invoke-pester 退出。如果所有测试都通过，或者测试失败的次数为 0，则该代码为 0。这在一些持续集成场景中很有用。

OutputFile 和 OutputFormat 参数将输出重定向到 NUnit xml 格式的文件。默认情况下，Pester 4.x 将使用 NUnit 格式输出使用 2.5 模式的结果。不能用-OutputFormat 更改 format 事件(只有一个可能的值 NUnitXml)。对于一些显示 Nunit 测试结果的 CI 工具来说，使用输出会很有用

## 代码覆盖率

代码覆盖率衡量测试执行代码的程度。该度量以百分比表示。覆盖率并不意味着模块或脚本没有错误，它仅仅意味着代码在测试过程中已经运行。

要使用 persted 生成代码覆盖率度量，您需要按照您需要测试的脚本的路径使用 invoke-persted with-coverage。

```
 invoke-pester  -script  .\function.test.ps1  -coverage  .\function.ps1 
```

Enter fullscreen mode Exit fullscreen mode

你可以使用通配符和一个 coma 来分析多个文件

```
 invoke-pester  -script  .\functions.test.ps1  -coverage  .\function.ps1,  .\*.psm1 
```

Enter fullscreen mode Exit fullscreen mode

您还可以为覆盖率使用哈希表。它必须包含路径密钥。您可以使用功能键将分析限制在这些功能上，也可以使用 StartLine 或 EndLine 来限制分析。

请注意，拥有 100%的覆盖率并不意味着代码没有 bug，这只是表明您的所有代码都已经在测试中执行了

## 高级用法

## 试驾

一个函数可能需要操纵文件系统，它可以创建、删除或修改一个或多个文件。在测试阶段改变文件系统是不可取的。请提供一个名为 TestDrive 的驱动器:。该驱动器在测试范围(描述或上下文)中可用，并在范围结束时自动删除。
当使用测试驱动器时，在$env:tmp 中创建一个随机文件夹，并使用它将当前测试驱动器中的文件放入作用域中。
你可以使用 testdrive:\或者＄test drive 变量在测试过程中执行任何文件操作。

```
Describe  "test"  {  new-item  (Join-Path  $TestDrive  'File.txt')  It  "Test if File.txt exist"  {  (test-path  -path  (Join-Path  $TestDrive  'File.txt')  )  |  Should  -Be  $true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 嘲讽

脚本和函数依赖于测试机上可能不存在的模块和函数，或者执行破坏性操作。
你设计了一个创建或删除活动目录帐户的功能。
您可能在测试计算机上没有任何活动目录模块，并且/或者您不想在生产计算机上进行任何更改
您创建了一个更改服务器配置的模块，并且您不想在测试阶段进行任何更改。
你只需要确保代码是正确的。

模仿在纠缠中，让你模仿在测试中调用的函数或命令的结果。使用 Mock 时，只需为给定的命令创建结果，这样就可以测试脚本的其他部分。

要模仿一个函数或命令，只需使用 mock 和函数或命令的名称。

```
Describe  "test"  {  Mock  remove-something  {  }  It  "Test if remove-something return null"  {  remove-something  |  Should  BeNullOrEmpty  }  } 
```

Enter fullscreen mode Exit fullscreen mode

模仿受到测试范围的限制，无论是描述还是上下文。如果你需要测试不同的行为，你可以使用-ParameterFilter 来创建不同的结果

```
Describe  "test"  {  Mock  Get-AswerAboutLifeUniverseEverything  {  return  42  }  -ParameterFilter  {  $Name  -eq  "42"  }  Mock  Get-AswerAboutLifeUniverseEverything  {  return  42  }  -ParameterFilter  {  $Name  -eq  "57"  }  Mock  Get-AswerAboutLifeUniverseEverything  {  return  42  }  It  "Test if Get-AswerAboutLifeUniverseEverything return 42"  {  Get-AswerAboutLifeUniverseEverything  |  Should  be  42  }  It  "Test if Get-AswerAboutLifeUniverseEverything -Name 42 return 42"  {  Get-AswerAboutLifeUniverseEverything  -Name  42  |  Should  be  42  }  It  "Test if Get-AswerAboutLifeUniverseEverything -Name 57 return 42"  {  Get-AswerAboutLifeUniverseEverything  -Name  57  |  Should  be  42  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 IT 块中使用 Mock，在这种情况下，Mock 将在父作用域中可用。

您可能想要测试在测试阶段是否调用了模拟命令。有两种方法可以测试这个

如果一个模拟命令被标记为可验证的，抛出一个错误

```
Describe  "test"  {  Mock  Get-AswerAboutLifeUniverseEverything  {  return  42  }  -Verifiable  It  "Test if remove-something return 42"  {  Get-AswerAboutLifeUniverseEverything  |  Should  be  42  }  Assert-VerifiableMocks  } 
```

Enter fullscreen mode Exit fullscreen mode

测试被模仿命令的执行的另一种方法是使用 Assert-MockCalled。Assert-MockCalled 必须放在 IT 块中。您需要将它与-CommandName 参数一起使用。该参数采用被模仿命令的名称

```
Describe  "test"  {  Mock  Get-AswerAboutLifeUniverseEverything  {  return  42  }  It  "Test if remove-something return 42"  {  Get-AswerAboutLifeUniverseEverything  |  Should  be  42  }  It  "Test if remove-something was called"  {  Assert-MockCalled  -CommandName  Get-AswerAboutLifeUniverseEverything  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用-times 参数控制模拟命令被调用的次数。Times X 参数测试被模仿的命令是否至少被调用了 X 次。如果你需要测试这个被模仿的命令只被调用了 X 次，你可以使用——确切地说

模仿函数或 cmdlet 时，可能需要返回一个对象。例如 Get-AdUser 返回一个微软。ActiveDirectory . management . aduser 对象。大多数时候你可以使用一个 PScustom 对象来模仿返回的对象

```
 Describe  "test"  {  Mock  Get-AdUser  {  $AObject  =  [PSCustomObject]@{  Surname  =  "Marc"  UserPrincipalName  =  "marc@mydomain.com"  Enabled  =  $true  SamAccountName  =  "marc"  ObjectClass  =  "user"  }  Return  $AdObject  }  -ParameterFilter  {  $Identity  -eq  "Marc"  }  It  "Test if the User is Valid"  {  (Get-AdUser  -identity  “Marc”).Enabled  |  Should  betrue  }  It  " Test if the User UPN "  {  (Get-AdUser  -identity  “Marc”).UserPrincipalName  |  Should  be  “marc@mydomain.com”  }  } 
```

Enter fullscreen mode Exit fullscreen mode

但是有时 PsCustomObject 是不够。当您在脚本中使用 class 时就是这种情况。你可以使用 New-MockObject 返回任何类型的对象。模拟任何类型的对象，而不需要创建它。你可以用它来嘲笑任何人。Net 类型或者你自己的基于类的类型

```
Mock  Get-Something  {  New-MockObject  -type  'System.Diagnostics.Process'  } 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，您可能需要在测试之前或之后执行一些代码。这可能是变量初始化或环境设置。

例如，您可以在测试驱动中创建一些文件，打开到 webservice 的连接，创建随机数据。BeforeAll 和 AfterAll 必须位于 Describe 或 Context 块中。它可以放在块内的任何地方，但最好放在开始的 BeforeAll 和结束的 All 之后。

BeforeEach 和 AfterEach 以与 BeforeAll/AfterAll 相同的方式在每个 IT 块上运行

## 模块测试

可以使用 Pester 来测试 PowerShell 模块。但是记住，纠缠需要访问代码来测试代码。

第一步，当模块加载到内存中时，您需要指示 PowerShell 移除该模块。否则，您可能无法测试代码的实际版本。

```
Get-module  -name  ‘TheModule’  -all  |  remove-module  -force  -erroraction  SilentContinue 
```

Enter fullscreen mode Exit fullscreen mode

您可以导入模块

```
Import-Module  -name  PathTo\TheModule.psd1  -force  -ErrorAction  Stop 
```

Enter fullscreen mode Exit fullscreen mode

问题，这样做您将只能访问 FunctionToExport 内部声明的公共函数。为了避免这种情况，你需要使用 InModuleScope

```
InModuleScope  TheModule  {  Describe  'Module Test'  {  It  'test one not exported function'  {  Get-NotExportFunction  |  Should  not  Throw  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这是我对纠缠的介绍。告诉我你的想法