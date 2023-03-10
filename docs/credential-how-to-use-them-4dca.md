# 凭证:如何使用它们？

> 原文：<https://dev.to/frpsug/credential-how-to-use-them-4dca>

声明:
本文由本人翻译。
我这样做是为了训练，但我的英语水平不是很好，所以我为他们可能出现的任何错误道歉。
如果真的一点都不理解，谢谢你指出来，这样我可以提高自己；-)

# 信息

这篇文章是为法国 Powershell 用户组的演示文稿而写的。

你可以在 Youtube 上找到这个演示: [FRPSUG 频道](https://www.youtube.com/watch?v=3OR143IPQ4o&t)

# 凭证的不同使用方式...

## 初始请求

自从我开始使用 PowerShell，我很快就在我的脚本中提出了凭证管理的问题

从可以用基本方式处理的简单需求到在自动脚本中使用凭证，我一直在寻找最好的方式来完成它。

## 处理请求

***1。获取-凭证*T3】**

使用凭证最简单的方法是使用基本的 PowerShell 命令

```
$cred  =  Get-Credential  -Message  "Message show in the Popup"  -UserName  MyUser 
```

Enter fullscreen mode Exit fullscreen mode

结果是

```
PS  >  $cred  UserName  Password  --------  --------  MyUser  System.Security.SecureString 
```

Enter fullscreen mode Exit fullscreen mode

该变量`$cred`可用于例如以下命令

```
Enter-PSSession  -ComputerName  MyComputer  -Credential  $cred 
```

Enter fullscreen mode Exit fullscreen mode

***2。ConvertFrom-SecureString:磁盘上的存储*T3】**

另一个更高级的解决方案是将密码存储在计算机的一个文件中。

当然，这种存储必须以安全的方式进行。
和之前一样，第一步是创建`$Cred`对象

```
$cred  =  Get-Credential  -Message  "Message show in the Popup"  -UserName  MyUser 
```

Enter fullscreen mode Exit fullscreen mode

第二步是将密码存储在文件中，但是要加密
，为此我们使用了`ConvertFrom-SecureString`命令

```
$Cred.Password  |  ConvertFrom-SecureString  |  Out-File  C:\temp\password.txt 
```

Enter fullscreen mode Exit fullscreen mode

在我的文件 c:\temp\password.txt 中，我的密码是这样的

```
01000000d08c9ddf0115d1118c7a00c04fc297eb0100000057670149ac674a41ad9d185a8a82724b0000000002000000000010660000000100002000000093aaaf1ed598a69bbfb4cc77e81dfeb2786f26db6184538833af18054ef1a8a3000000000e800000000200002000000098c97f4f344d0159f337966d55060ad3297cae7515938457a713ddd9eaac5cdf200000003d986891fb27cb3983f798082083ac734d97d6235a186d3cc43db26f63bd44684000000018620d4739c0a26a6261e8c9867e94605cd35c61090c982999d5bb09fb54ec7d9a3499ebeb304c67720edfa37a34fe7fd4bce8fd8468dbee5081f56c81f4ce46 
```

Enter fullscreen mode Exit fullscreen mode

要使用这个加密的密码，您必须先解密它。
为此，我们将使用`ConvertTo-SecureString`命令
。之后，使用解密后的密码，我们可以创建一个新的 PSCredential 对象并使用它

```
$Username  =  "MyUser"  $SecurePassword  =  Get-Content  c:\temp\password.txt  |  ConvertTo-SecureString  $Cred  =  New-Object  System.Management.Automation.PSCredential  -ArgumentList  $Username,$SecurePassword 
```

Enter fullscreen mode Exit fullscreen mode

```
PS  >  $cred  UserName  Password  --------  --------  MyUser  System.Security.SecureString 
```

Enter fullscreen mode Exit fullscreen mode

和第一点一样，我们有一个对象`$Cred`，可以在下面的脚本
中使用

```
Enter-PSSession  -ComputerName  MyComputer  -Credential  $cred 
```

Enter fullscreen mode Exit fullscreen mode

***3。Export-Clixml:磁盘上的存储*T3】**

这种方法的优点是，您可以利用 PowerShell 的多功能性来确保数据不仅可以导出，还可以使用安全字符串安全地存储。应该注意，这些凭证文件只能由同一系统上的同一用户打开。

为了创建导出文件，我们将使用`Export-Clixml`命令

```
get-credential  -message  "User's Password ?"  -UserName  MyUser  |  Export-Clixml  -Path  "c:\temp\user.xml" 
```

Enter fullscreen mode Exit fullscreen mode

在文件 c:\temp\user.xml 中，我们可以看到以下信息

```
<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>System.Management.Automation.PSCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>System.Management.Automation.PSCredential</ToString>
    <Props>
      <S N="UserName">MyUser</S>
      <SS N="Password">01000000d08c9ddf0115d1118c7a00c04fc297eb0100000057670149ac674a41ad9d185a8a82724b00000000020000000000106600000001000020000000dadd8864c9b930a2eb07a6745ac4fb5711912c318c401f7e35bb91d4d1cc180b000000000e8000000002000020000000b5a862ba266c236357445b773ca38d73ed124cf82d863ac4c11e2b48d57fca4b2000000054180930ba9fd53a6c4bdd9d7f69c044c88072b0d411486bccc1ca3cca417bf440000000d6197eafe8a133235bd1b44e376c3ff02e94da9f39b7d24b9a68ef5dbd629e44180ce15c3e67830d758fa1296f60a98cb2371ef915990c921e728f44c72c4cbd</SS>
    </Props>
  </Obj>
</Objs> 
```

Enter fullscreen mode Exit fullscreen mode

要使用这些信息，我们必须使用反向命令`Import-Clixml`

```
 $Cred  =  Import-Clixml  -Path  "c:\temp\user.xml" 
```

Enter fullscreen mode Exit fullscreen mode

我们再次恢复一个对象`$Cred`

```
PS  >  $cred  UserName  Password  --------  --------  MyUser  System.Security.SecureString 
```

Enter fullscreen mode Exit fullscreen mode

今天，我倾向于使用 XML 文件的第三种解决方案