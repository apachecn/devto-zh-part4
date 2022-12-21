# PowerShell、文件和 Azure 存储帐户 blobs

> 原文：<https://dev.to/omiossec/powershell-files-and-azure-storage-account-blobs-5g8>

Azure Storage Blob 是一个在云中存储任何类型文件的经济高效的解决方案。它安全、可靠且富有弹性。此外，当你添加一个文件时，Blob 可以用作 Azure 函数的触发器，并且可以使用不同的定价层。你可以将它与应用程序一起使用，用于你在 Azure 中的部署，或者从你的文件服务器中存档文件。

你有几个选项来发送文件到 blob 容器，你可以使用 databox 如果你需要存档 Tb 的文件或 databox edge 来链接你的文件服务器到 Azure，你可以使用数据浏览器来复制文件。但是，如果您需要根据一种模式归档文件并保持文件夹结构，您能做些什么呢？

假设我们有一个包含几个文件的本地文件夹。您希望仅上传超过两周的 jpg 文件，并保留容器内的文件夹结构。该流程需要每天或每周运行。

为了执行这些操作，我使用 PowerShell 核心和 Azure AZ 的跨平台模块

第一步，我们需要创建一个存储帐户和一个 blob 容器。

请记住，Azure 中的存储帐户必须有一个全球唯一的名称。我使用一个随机的字符串来创建存储名称，并使用标记 DisplayName 作为人类阅读名称。

```
$LowercaseLetterCodes  =  (97..122)  $StorageAccountName  =  -join  ((Get-Random  -InputObject  $LowercaseLetterCodes  -Count  24)  |  Foreach-Object  {[char]$_}  )  $StorageAccountName  =  -join  ((Get-Random  -InputObject  $LowercaseLetterCodes  -Count  24)  |  Foreach-Object  {[char]$_}  ) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要创建一个 blob 容器

```
New-AzStorageContainer  -Name  "archive"  -Context  $AzStorageAccount.Context  -Permission  off  |  out-null 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个 SAS 令牌密钥

```
$AzStorageSasToken  =  New-AzStorageContainerSASToken  -container  "archive"  -Permission  rwdl  -ExpiryTime  (get-date).AddMonths(3)  -Context  $AzStorageAccount.Context 
```

Enter fullscreen mode Exit fullscreen mode

稍后将使用此密钥将文件上载到 blob 容器。

第二步，我们需要选择想要上传的文件。

请记住，我们需要获取超过两周的 JPG 文件。只列出 JPG 文件。我们可以使用–过滤器。这是实现目标的更有效的方法。但是 param 只接受单个字符串，并且只支持*和？

JPG 文件至少有两种扩展名，jpeg 和 jpg。滤波器参数可以这样设计:

```
-filter  "*.jp*g" 
```

Enter fullscreen mode Exit fullscreen mode

我们需要扫描整个目录，而不仅仅是当前文件夹，因此我们需要使用–recurse 开关。

我们还可以使用–Attributes 参数来排除目录、临时文件和加密文件。

```
-Attributes  !Directory+!System+!Encrypted+!Temporary 
```

Enter fullscreen mode Exit fullscreen mode

没有根据日期过滤文件的直接方法。唯一的方法是过滤输出。在 PowerShell 中，这意味着默认情况下使用管道 PowerShell 在生成对象时将对象发送到管道。大多数时候你不必关心这个，但是对于一个大的集合，你可以管理缓冲区。对象将作为批处理发送到管道。

缓冲区的大小取决于批处理的大小，我选择 100 是因为我希望文件夹中的文件数量少于 5 %。

为了防止内存增长，我们需要使用 select-object 来限制子项输出。我们只需要每个文件的路径、名称和长度

```
 |  select-object  FullName,Name,Length 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以过滤结果，从文件对象中，我们可以使用 LastWriteTime 元数据只获取超过 2 周的文件

```
|  where-object  LastWriteTime  -lt  $dateToday.AddDays(-15) 
```

Enter fullscreen mode Exit fullscreen mode

下面是最后的命令

```
$FilesListObject  =  get-childitem  -Path  $Path  -Attributes  !Directory+!System+!Encrypted+!Temporary  -Recurse  -filter  "*.jp*g"  -ErrorAction  SilentlyContinue  |  select-object  FullName,Name,LastWriteTime,Length  -OutBuffer  100  |  where-object  LastWriteTime  -lt  $dateToday.AddDays(-15) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始向 Azure 发送文件了。我们需要在第一步中创建的 AZ 模块、存储帐户名、容器名和 SAS 令牌。

首先，我们需要创建一个 Azure 存储环境

```
$AzureStorageContext  =  New-AzStorageContext  $StorageAccountName  -SasToken  $AzStorageSasToken 
```

Enter fullscreen mode Exit fullscreen mode

我们需要保留文件夹结构。对于每个文件，我们需要重新创建主文件夹的相对路径。

我们的主文件夹在$Path 变量中，而$FilesListObject 包含每个文件的名称和完整路径。我们只需要减去每个文件的完整路径的$Path。

```
foreach  ($FileInfo  in  $FilesListObject)  {  $FileRelativePath  =  Split-Path  -Path  $FileInfo.FullName  $VirtualPath  =  (Split-Path  $path  -Leaf)  +  ($FileRelativePath.Substring($Path.Length,  ($FileRelativePath.Length  -  $Path.Length)))  +  "\"  +  $FileInfo.Name  } 
```

Enter fullscreen mode Exit fullscreen mode

我们将在 set-azStorageBlobContent 的–blob 参数中使用该路径，但是我有一个小问题。法国人爱到处放口音(我们也怀念 H 和 S，不过那是后话了)。我需要某种正常化。

我们可以使用 System.textNormalization 和一个字符串生成器

```
[System.Text.NormalizationForm]$NormalizationForm  =  "FormD"  $Normalized  =  ($FileInfo.Name).Normalize($NormalizationForm)  $NormalizedFileName  =  New-Object  -TypeName  System.Text.StringBuilder  $normalized.ToCharArray()  |  ForEach-Object  -Process  {  if  ([Globalization.CharUnicodeInfo]::GetUnicodeCategory($psitem)  -ne  [Globalization.UnicodeCategory]::NonSpacingMark)  {  [void]$NormalizedFileName.Append($psitem)  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用 set-azStorageBlobContent
向 Azure 发送文件

```
foreach  ($FileInfo  in  $FilesListObject)  {  [System.Text.NormalizationForm]$NormalizationForm  =  "FormD"  $Normalized  =  ($FileInfo.Name).Normalize($NormalizationForm)  $NormalizedFileName  =  New-Object  -TypeName  System.Text.StringBuilder  $normalized.ToCharArray()  |  ForEach-Object  -Process  {  if  ([Globalization.CharUnicodeInfo]::GetUnicodeCategory($psitem)  -ne  [Globalization.UnicodeCategory]::NonSpacingMark)  {  [void]$NormalizedFileName.Append($psitem)  }  }  $FileRelativePath  =  Split-Path  -Path  $FileInfo.FullName  $VirtualPath  =  (Split-Path  $path  -Leaf)  +  ($FileRelativePath.Substring($Path.Length,  ($FileRelativePath.Length  -  $Path.Length)))  +  "\"  +  $NormalizedFileName.ToString()  Set-AzStorageBlobContent  -File  $FileInfo.FullName  -Container  "archive"  -Context  $AzureStorageContext  -Blob  $VirtualPath  -Force  } 
```

Enter fullscreen mode Exit fullscreen mode