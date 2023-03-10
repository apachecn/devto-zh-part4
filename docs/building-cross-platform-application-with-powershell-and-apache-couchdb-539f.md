# 用 Powershell 和 Apache CouchDB 构建跨平台应用程序

> 原文：<https://dev.to/matteoguadrini/building-cross-platform-application-with-powershell-and-apache-couchdb-539f>

# CouchDB 数据库

Apache CouchDB 是一个非 sql 数据库，在 HTTP 和 JSON API 上实现。

## 优势

这种类型的数据库是以`database -> documents -> revision_document -> data`的形式构建的。这允许版本化的文档。但这不是唯一的优势；CouchDB 中的复制确实是区别于其他数据库的杀手级特性。

# (电源)外壳

[Powershell](https://docs.microsoft.com/en-us/powershell/) 是在。NET 核心，是一个带有类型化对象的外壳。PowerShell 承诺彻底改变系统管理和
命令行 Shell 的世界。从基于对象的管道到以管理员为中心，再到深入到其他 Microsoft 管理技术，PowerShell 极大地提高了管理员和高级用户的工作效率。

## PSCouchDB 模块

要与 CouchDB 接口，您需要安装一个 powershell 模块: [PSCouchDB](https://matteoguadrini.github.io/PSCouchDB/) 。

安装简单；或者使用 git ( [文档](https://pscouchdb.readthedocs.io/en/latest/intro.html#installation) ):

```
git  clone  https://github.com/MatteoGuadrini/PSCouchDB.git  cd  PSCouchDB  # for Windows  copy  /Y  PSCouchDB  %Windir%\System32\WindowsPowerShell\v1.0\Modules  # for Unix  cp  -var  PSCouchDB  /usr/local/share/powershell/Modules 
```

或者 [PowershellGallery](https://www.powershellgallery.com/packages/PSCouchDB) :

```
Install-Module  -Name  PSCouchDB 
```

### 测试 pscouchdb 模块

这样，您可以在 PSCouchDB 模块中发现所有可用于 CouchDB 的 cmdlets。

```
Search-CouchDBHelp  -Pattern  . 
```

### 演示

[![asciicast](img/3fa9db33625ed15cf436182f7615df6b.png)](https://asciinema.org/a/232696)

# 配置 CouchDB 集群

是时候配置我们的数据库了。我们开始决定哪种类型的集群将是我们的数据库。对于一个简单的配置，就像在我们的例子中，我们选择一个单节点集群。

## 管理员

但是首先，您需要创建一个可以读写数据库的管理员用户。(没有管理员，任何人都可以读写各种数据库；请参阅[管理团队](https://docs.couchdb.org/en/stable/intro/security.html#the-admin-party)指南。)
创建*密码*和*管理员*用户:

```
$pwd  =  "password"  |  ConvertTo-SecureString  -AsPlainText  -Force  New-CouchDBAdmin  -Userid  admin  -Password  $pwd 
```

测试管理员用户已创建:

```
Get-CouchDBAdmin  -Authorization  admin:password 
```

## 启用集群

现在，启用我们的单集群模式:

```
Enable-CouchDBCluster  -SingleNode  -Authorization  admin:password 
```

验证集群已经创建，验证他创建了三个数据库: **_global_changes** 、 **_replicator** 、 **_users**

```
Get-CouchDBDatabase 
```

## 为测试创建一个数据库

我们创建一个测试数据库来验证一切正常:

```
New-CouchDBDatabase  -Database  test  -Authorization  admin:password 
```

# 创建应用程序

应用程序通常解决一个实际问题。在这种情况下，让我们想象一下，我们必须调查我们公司的硬件。我们需要一个每隔几分钟就从 Active Director 上的客户端获取信息并将其放入数据库的应用程序。

## 简单的机器盘点

### 创建数据库

让我们从创建将存放机器信息的数据库开始:

```
New-CouchDBDatabase  -Database  hw_inventory  -Authorization  admin:password 
```

### 创建一个 get 函数

让我们从编辑名为 *HardwareInventory.ps1* 的文件开始，用这个函数:

```
function  Get-ComputerInventory  ($Authorization)  {  # Create an Active Directory session  $session  =  New-PSSession  -ComputerName  "your_domain_controller.local"  Import-PSSession  -Session  $session  -module  ActiveDirectory  # Get all computer  $AllComputers  =  Get-ADComputer  -Filter  *  -Properties  DNSHostName  foreach  ($ComputerName  in  $AllComputers.DNSHostName)  {  $info  =  @{}  # Test connection of computer  if  (Test-Connection  $ComputerName  -Count  1  -Quiet)  {  # Select various info  $info.Add('ComputerHW',  (Get-CimInstance  -Class  Win32_ComputerSystem  -ComputerName  $ComputerName  |  select  Manufacturer,  Model,  NumberOfProcessors,  @{Expression={$_.TotalPhysicalMemory  /  1GB};Label="TotalPhysicalMemoryGB"}))  $info.Add('ComputerCPU',  (Get-CimInstance  win32_processor  -ComputerName  $ComputerName  |  select  DeviceID,  Name,  Manufacturer,  NumberOfCores,  NumberOfLogicalProcessors))  $info.Add('ComputerDisks',  (Get-CimInstance  -Class  Win32_LogicalDisk  -Filter  "DriveType=3"  -ComputerName  $ComputerName  |  select  DeviceID,  VolumeName,  @{Expression={$_.Size  /  1GB};Label="SizeGB"}))  $info.Add("timestamp",  (Get-Date  -f  MM-dd-yyyy_HH_mm_ss))  # Write on database  if  (Get-CouchDBDocument  -Database  hw_inventory  -Document  $ComputerName  -ErrorAction  SilentlyContinue  -Authorization  $Authorization)  {  Set-CouchDBDocument  -Database  hw_inventory  -Document  $ComputerName  -Data  $info  -Revision  $(Get-CouchDBDocument  -Database  hw_inventory  -Document  $ComputerName)._rev  -Replace  }  else  {  New-CouchDBDocument  -Database  hw_inventory  -Document  $ComputerName  -Data  $info  }  }  }  Get-PSSession  |  Remove-PSSession  } 
```

通过此功能，我们可以通过 WMI:
获取我们域内每台机器的基本信息

```
$info = @{}
$info.Add('ComputerHW', (Get-CimInstance -Class Win32_ComputerSystem -ComputerName $ComputerName |
            select Manufacturer,
                   Model,
                   NumberOfProcessors,
                   @{Expression={$_.TotalPhysicalMemory / 1GB};Label="TotalPhysicalMemoryGB"}))
$info.Add('ComputerCPU', (Get-CimInstance win32_processor -ComputerName $ComputerName |
            select DeviceID,
                   Name,
                   Manufacturer,
                   NumberOfCores,
                   NumberOfLogicalProcessors))
$info.Add('ComputerDisks', (Get-CimInstance -Class Win32_LogicalDisk -Filter "DriveType=3" -ComputerName $ComputerName |
            select DeviceID,
                   VolumeName,
                   @{Expression={$_.Size / 1GB};Label="SizeGB"}))
$info.Add("timestamp", (Get-Date -f MM-dd-yyyy_HH_mm_ss)) 
```

并写入数据库:

```
# Write on database
if (Get-CouchDBDocument -Database hw_inventory -Document $ComputerName -ErrorAction SilentlyContinue -Authorization $Authorization) {
    Set-CouchDBDocument -Database hw_inventory -Document $ComputerName -Data $info -Revision $(Get-CouchDBDocument -Database hw_inventory -Document $ComputerName)._rev -Replace
} else {
    New-CouchDBDocument -Database hw_inventory -Document $ComputerName -Data $info
} 
```

### 创建查找功能

一旦我们记住了数据，我们也需要以某种方式寻找它们；然后我们必须在我们的文件 *HardwareInventory.ps1* 中创建一个函数来搜索数据库:

```
# Find computer into inventory  function  Find-ComputerInventory  ()  {  param(  [Parameter(mandatory=$true)]  [string]  $Computername  )  $docs  =  Find-CouchDBDocuments  -Database  hw_inventory  -Selector  "_id"  -Operator  regex  -Value  ".*$Computername.*"  -Fields  _id,ComputerHW,ComputerCPU,ComputerDisks,timestamp  $docs.docs  } 
```

此功能允许您搜索机器名称并提取我们感兴趣的数据。

### 准备功能

此时，我们将我们的文件保存并包含在机器的所有用户的 *profile.ps1* 中:

```
". path_of_file/Hardware.ps1"  >>  $PsHome\Profile.ps1 
```

### 使用和调度功能

现在是时候安排每一个小时收集第一批数据了，用这个命令:

```
# if use a powershell 6 or high, use pwsh  powershell  -Command  "Get-ComputerInventory -Authorization admin:password" 
```

### 找机器

让我们等几个小时，找一辆车，看看一切是否正常:

```
Find-ComputerInventory  -ComputerName  machine1 
```

# 结论

在 10 分钟内，我们在 powershell 中创建了一个机器库存应用程序，它安全地连接到 CouchDB。

# 参考文献

[Powershell docs](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-6)
[couch db docs](https://docs.couchdb.org/en/stable/)
[PSCouchDB docs](https://pscouchdb.readthedocs.io/en/latest/)
[PSCouchDB github](https://github.com/MatteoGuadrini/PSCouchDB)

[捐款](https://pscouchdb.readthedocs.io/en/latest/support.html#donations)