# Azure DevOps YAML 构建包含多个项目的 Mono 存储库

> 原文：<https://dev.to/nikolicbojan/azure-devops-yaml-build-for-mono-repository-with-multiple-projects-146g>

我将向您展示我如何在 Azure DevOps 中为我们的 Mono 存储库设置 YAML 版本，该存储库包含作为一个产品一部分的多个(大约 20 个)服务。

> TL；DR；访问 Git 库[https://github.com/nikolic-bojan/azure-yaml-build](https://github.com/nikolic-bojan/azure-yaml-build)T3】它包含了 3 个服务的 YAML 构建的工作样本。

# 移动到单声道储存库

我们有大约 20 个不同的服务(REST APIs)，它们前面有一个网关，将调用路由到这些服务和编排。您可以称之为微服务架构，也可以不称之为微服务架构，但是我们现在不讨论这个问题。

它是这样组织的:

*   每个服务都有自己的存储库
*   用于服务间通信的每个模型 NuGet(包含共享的请求/响应类)都有自己的存储库

因此，创建和维护了大约 40 个存储库和相同数量的构建定义。另外，我们是作为一个小团队来做 GitFlow 的。噩梦！

当我终于有时间的时候，我知道我们应该去哪里——Mono repository。我不想让你相信这是为每个人准备的，但是如果你已经(或者正在路上)需要帮助，请继续阅读。

这是如何做到的故事。

## 新建知识库

创建一个新的存储库很容易，但是移动代码呢？复制/粘贴——不行，我们会丢失所有的 Git 历史。在 Sourcetree 的帮助下，我在网上找到了一些不错的文章(我不是 Git 控制台的家伙。Yet)和 Git console(你无法避免)，下面是我对每个项目做**的步骤。当然，要确保您在新存储库的主分支中:**

1.  在 Sourcetree 中添加远程存储库，指向您的**旧**项目的存储库，并将其命名为 **repo**
2.  执行以下 GIT 命令`git merge repo/master --allow-unrelated-histories`。当然，选择从 master 或任何其他旧回购的分支机构。使用允许无关的历史非常重要，否则将不起作用。
3.  在新存储库中为您的服务创建一个文件夹。你不希望所有的项目都在同一个文件夹中，对吗？
4.  将所有文件移动到新文件夹。
5.  解决冲突，如果有的话。
6.  推动变革。
7.  锁定旧存储库的分支，以防止一些开发人员继续在那里工作。

如果您有一些特性分支(并不是所有新代码都在 master 上)，您可以在一个新的存储库上创建新的分支，并将文件从旧的存储库复制到该服务的文件夹中。Git 会像处理变更一样处理它，你可以推动它们。您将丢失特性分支上一些提交历史，但是您可以接受。

通过这种方式，我将所有的服务转移到了一个新的存储库中，但是在这 7 个步骤之后，我还做了一些与为每个服务构建定义相关的事情。不要急着马上搬家。逐个服务地移动。

## 构建定义修改

所有东西现在都在同一个存储库中。此外，所有服务都在它们的新文件夹中。现在我们来看问题——我们需要改变我们的构建定义，当然，这取决于它是如何被构建的。

我接下来要做的是:

*   仅允许在**主机**上触发，并且仅适用于服务所在的文件夹。我只是想看看新仓库的构建工作。
*   更改所有指向*的步骤/变量。sln 或*。csproj 文件遵循新的文件夹结构，以构建你想要的，而不是整个织补的东西。
*   尝试构建。构建服务，将它放在较低的环境中，并进行回归测试。一切都需要井井有条。
*   现在，您可以迁移下一个服务。

## 型号编号

记得我提过这些吗？这些都很简单。仅包含 POCO 模型的. NET Standard 2.0 项目。我决定将它们移到服务的解决方案中，因为它们代表了该服务的**接口**。

所有其他需要它们的服务(调用该服务)也直接引用它们。这是同一个存储库！

我不是想在那里保留历史。复制/粘贴做到了。但是这是可能的，就像我们在服务中所做的那样。

之后的回归都是绿色的。这是最重要的。

# 一个人建造来统治他们所有人

我对只在**主**上有建造触发器不太满意。我本可以在所有分支上设置触发器，但我想要更多的控制。

如果我想构建 2 或 3 个服务，而其中一个服务的代码发生了变化(还记得那些模型项目吗？).我知道，这不是完全的分离，但毕竟，把东西放在一个存储库中的意义是因为它们是联系在一起的。

我也不热衷于维护 20+的版本。我想控制一个地方。

## PowerShell + YAML 到 resque！

通过 UI 设置 CI 来知道何时触发和构建哪些服务是非常麻烦的。是的，我说的是“服务”，不是服务。这是由于我希望事情如何运作。如果我一次对几个服务进行更改，我希望它们都被构建。有时，如果我对 1 个服务进行更改，我希望构建 2 个服务。

好的一面是，我们所有的构建都有相同的步骤，我们将一个构建参数化，因此它可以很容易地被克隆，并使用变量为特定的服务进行专门化。

首先，创建一个新的构建定义。您需要选择 YAML 版本。在那里，您应该输入以下内容:

```
trigger:
  branches:
    include:
    - '*'

resources:
- repo: self

jobs:
- job: Build_Queue
  steps:  
  - task: powershell@2
    name: setBuildQueue
    inputs:
      targetType: filePath
      filePath: ./build/get-service-queue.ps1
    displayName: 'Get  Service  Build  Queue'    

- template: build-template.yml
  parameters:
    name: Service_01
    solutionFolder: 'service1'
    projectFile: '/service1.api/service1.api.csproj'

- template: build-template.yml
  parameters:
    name: Service_02
    solutionFolder: 'service2'
    projectFile: '/service2.api/service2.api.csproj'

- template: build-template.yml
  parameters:
    name: Service_03
    solutionFolder: 'service3'
    projectFile: '/service3.api/service3.api.csproj'

# Add more template jobs with parameters for other Services 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我刚才做的是:

1.  在所有的分支上设置**触发器。**
2.  将存储库设置为自身。
3.  创建了一个 PowerShell 作业来获取要构建的服务列表。
4.  定义为为我的每个服务运行构建模板(这里只有 3 个)

将会发生的是，PowerShell 脚本将确定应该构建哪些服务(自定义逻辑),并以分号(；)分开的服务文件夹名(不完善，还在打磨那个，我的 PowerShell 是初学者水平)。

```
$global:buildQueueVariable  =  ""  $global:buildSeparator  =  ";"  Function  AppendQueueVariable([string]$folderName)  {  $folderNameWithSeparator  =  -join($folderName,  $global:buildSeparator)  if  ($global:buildQueueVariable  -notmatch  $folderNameWithSeparator)  {  $global:buildQueueVariable  =  -join($global:buildQueueVariable,  $folderNameWithSeparator)  }  }  if  ($env:BUILDQUEUEINIT)  {  Write-Host  "Build Queue Init: $env:BUILDQUEUEINIT"  Write-Host  "##vso[task.setvariable variable=buildQueue;isOutput=true]$env:BUILDQUEUEINIT"  exit  0  }  # Get all files that were changed  $editedFiles  =  git  diff  HEAD  HEAD~  --name-only  # Check each file that was changed and add that Service to Build Queue  $editedFiles  |  ForEach-Object  {  Switch  -Wildcard  ($_  )  {  "service1/*"  {  Write-Host  "Service 1 changed"  AppendQueueVariable  "service1"  }  "service2/*"  {  Write-Host  "Service 2 changed"  AppendQueueVariable  "service2"  }  "service3/*"  {  Write-Host  "Service 3 changed"  AppendQueueVariable  "service3"  AppendQueueVariable  "service2"  }  # The rest of your path filters  }  }  Write-Host  "Build Queue: $global:buildQueueVariable"  Write-Host  "##vso[task.setvariable variable=buildQueue;isOutput=true]$global:buildQueueVariable" 
```

Enter fullscreen mode Exit fullscreen mode

这是逻辑:

1.  我首先检查是否有人在构建时设置了 **buildQueueInit** 变量。我想有选择地设置手动构建哪些服务。如果是，我设置 **buildQueue** output(对 **output** 非常重要)变量并跳过 GIT diff 部分。
2.  如果没有设置 **buildQueueInit** ,我将执行 GIT diff，获取在最后一次提交中被更改的文件，并找出它们在哪个文件夹中，这样我就可以列出被更改的服务。然后我用那个值设置 **buildQueue** 输出变量。

GIT diff magic 是从 Taul 的答案中提取出来的，它会获取上次提交
[时更改的所有文件 https://stack overflow . com/questions/53227343/trigging-azure-devo PS-builds-based-on-changes-to-sub-folders/53837840 # 53837840](https://stackoverflow.com/questions/53227343/triggering-azure-devops-builds-based-on-changes-to-sub-folders/53837840#53837840)

## 构建模板

好了，拼图的最后一块-构建模板。我通过点击我现有构建管道的**查看 YAML** 按钮来获取它。然后我不得不稍微调整一下。下面是它的第一部分(我只保留了示例的恢复任务):

```
parameters:
    name: '--'
    BuildConfiguration: 'Release'
    BuildPlatform: 'any  cpu'
    solutionFolder: '--'
    projectFile: '/Api/Api.csproj'
    RestoreBuildProjects: '$(project.file)'    

jobs:
- job: ${{ parameters.name }}
  dependsOn: Build_Queue
  continueOnError: true
  variables:
    BuildConfiguration: ${{ parameters.BuildConfiguration }}
    BuildPlatform: ${{ parameters.BuildPlatform }}
    project.file: ${{ parameters.solutionFolder }}${{ parameters.projectFile }}
    Parameters.RestoreBuildProjects: ${{ parameters.RestoreBuildProjects }}
    myBuildQueue: $[ dependencies.Build_Queue.outputs['setBuildQueue.buildQueue'] ]
  condition: and(succeeded(), contains(dependencies.Build_Queue.outputs['setBuildQueue.buildQueue'], '${{ parameters.solutionFolder }}'))
  steps:
  - task: powershell@2
    inputs:
      targetType: inline
      script: 'Write-Host  "Build  Queue  init:  $(buildQueueInit)  and  from  parameters  $(myBuildQueue)"'

  - task: DotNetCoreCLI@2
    displayName: Restore
    inputs:
      command: restore
      projects: '$(Parameters.RestoreBuildProjects)' 
```

Enter fullscreen mode Exit fullscreen mode

参数是模板的输入参数。你还记得我们使用的**名称**和**解决方案文件夹**吗？

变量是用于工作的，我们读取一些输入参数来设置它们。

这里重要的是 **myBuildQueue** ，它包含了应该构建的所有服务的完整列表。
更重要的是**条件**，它检查整个作业是否应该被执行。它检查 **solutionFolder** 是否在那个用分号分隔的列表中，该列表是在那个疯狂的 PowerShell 脚本中填充的。

如果匹配-将运行该作业中的任务。否则，它将跳过整个作业。

在 PowerShell 脚本中，您可以定义服务具有依赖关系，因此您可以说，如果服务 3 发生了变化，您也应该构建服务 2。这就是我在脚本的这一部分中为服务 3 所做的。

```
Switch  -Wildcard  ($_  )  {  "service3/*"  {  Write-Host  "Service 3 changed"  AppendQueueVariable  "service3"  AppendQueueVariable  "service2"  } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您可以在脚本中设置一些有趣的规则，以便定义应该在哪些变更的基础上构建什么。

现在，您只需要维护一个构建！

# 手动队列构建，包含要构建的服务列表

记得我提到过 **buildQueueInit** 并且你设置了一个变量。以下是在 Azure DevOps 中执行此操作的步骤:

1.  转到您的管道->构建，并选择您的 YAML 构建
2.  点击编辑构建，你会看到一个按钮变量
3.  点击它，然后+创建一个新的
4.  将其名称设置为 **buildQueueInit** 并选中**让用户在运行此管道时覆盖此值**框

现在，当您手动对构建进行排队时，您可以在其中添加一个值，例如**service1；服务 2；**服务 1 和服务 2 将为您构建。

您确实希望这样做的一个场景可能是对于某些发布分支，您不希望基于最后一次提交自动触发内容。你想决定“这些服务将在这次冲刺后发布”。

感谢阅读！