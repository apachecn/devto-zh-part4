# 使用 VBA 宏保护和取消保护工作表 Excel 工作表(Excel Online)

> 原文：<https://dev.to/nirmal_kumar/protect-unprotect-sheets-excel-sheets-using-vba-macros-excel-online-5ga9>

[![](img/1556f1dc53c019c17717adda6aef1324.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ST1ycYG1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1561612510-79b98876075e%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1500%26q%3D80)

### Excel Online

随着现代浏览器和网络标准的出现，用户已经转而使用电子表格和文字处理器的在线版本，可以很容易地从多种设备和平台访问它们。但是我们仍然不能忘记使用 VBA 脚本编写的宏的威力，这些宏主要是针对 Excel 和 Word 的。

我们一直使用 Excel 电子表格作为我们的一个 Sharepoint 应用程序的数据库。该应用程序生成多个电子表格，并通过 Excel Online 向用户提供访问。我们希望根据角色和用户指定来保护一些文件。似乎当前 Excel online 不支持工作表保护/取消保护。在 [Excel UserVoice](https://excel.uservoice.com/forums/274580-excel-online/suggestions/15642678-enable-protecting-unprotecting-worksheets-in-excel) 中也有 183 人要求这个功能。在这篇文章中，我分享了同样的解决方法。

### 保护/解除保护的宏解决方案

我们采用的解决方法是在 Sharepoint 应用程序中启用 OneDrive Sync，然后创建一个宏来保护/取消保护所有 100 多个文件。这将通过 OneDrive 同步实用程序立即同步到在线云。当您打开 Excel Online 并尝试编辑该文件时，您将得到一条消息“该工作表受保护”

这里的宏保护/保护 OneDrive 文件夹中给定文件夹中的所有文件。

```
Function ApplyProtection(sourceFolder as String,sheetName as string, isUnProtect As Boolean)

    Dim sheetPassword As String
    sheetPassword="YOUR SHEET PASSWORD"

    Dim actualFile As String

    Dim selFile As String
    actualFile = Dir(sourceFolder & "\*.xlsx")

    Do While Len(actualFile) > 0

        Application.ScreenUpdating = False
        Set wbResults = Workbooks.Open(filename:=sourceFolder & "/" & actualFile, UpdateLinks:=0, ReadOnly:=False)

        Dim reqSheet As Worksheet

        Set reqSheet = wbResults.Sheets(sheetName)
        Dim sheetState As Boolean

        sheetState = SheetProtected(reqSheet)

        If sheetState = False And isUnProtect = False Then
                reqSheet.Protect Password:=sheetPassword
        End If

        If sheetState = True And isUnProtect = True Then
                reqSheet.Unprotect Password:=sheetPassword
        End If

        actualFile = Dir
    Loop

End Function

​    
Private Function SheetProtected(TargetSheet As Worksheet) As Boolean
     'Function purpose:  To evaluate if a worksheet is protected

    If TargetSheet.ProtectContents = True Then
        SheetProtected = True
    Else
        SheetProtected = False
    End If

End Function

'Function Usage. Create Macro "ProtectFiles" and bind this ProtectAllFiles Module

Sub ProtectAllFiles()

Dim sourceFolder As String
sourceFolder="PUT YOUR SOURCE FOLDER PATH"
Call ApplyProtection(sourceFolder,False)

End Sub

'Function Usage. Create Macro "UnProtectFiles" and bind this UnProtectAllFiles Module
Sub UnProtectAllFiles()

Dim sourceFolder As String
sourceFolder="PUT YOUR SOURCE FOLDER PATH"
Call ApplyProtection(sourceFolder,True)

End Sub 
```

希望这对想要保护 Excel 网络版工作表的人有所帮助。

快乐脚本。