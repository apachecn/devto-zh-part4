# 在 SIF 中记录生成的密码

> 原文：<https://dev.to/jermdavis/logging-generated-passwords-in-sif-562p>

最近，我一直在考虑为生产部署调整 SIF 脚本，并意识到有时您希望 SIF 为您生成随机密码，但您需要记录它们，以便您可以在为其他角色编写的脚本中重用它们。它不是开箱即用的，但事实证明它其实很简单:

“`XPx-SingleDeveloper.json`”的默认配置包括一个为您生成一组随机密码(和一些其他东西)的任务:

```
 "Tasks": {
        "GeneratePasswords": {
            "Description": "Generates all shared passwords and secrets.",
            "Type": "SetVariable",
            "Params": {
                "Name": "XP1Passwords",
                "Scope": "Global",
                "Value":  [
                    /* Other generation tasks... */

                    {"SqlCorePassword":                "[variable('SqlCore.Password')]"},

                    /* Other generation tasks... */
                ]
            }
        }
    } 
```

如果你觉得自己太懒而不能生成所有这些东西，那么在所有东西都不在同一个盒子里的脚本中重用它似乎是明智的…

它将所有这些值存储到变量中，以便它们可以在设置所有角色的所有包含任务中重用——但是它似乎没有在其他任何地方记录它们。从安全的角度来看，这是完全合理的——但是如果你不得不将内容传递到另一台服务器上并在那里运行，这就没有任何帮助了。(是的，我知道 SIF v2 支持远程处理——但是到目前为止，我还没有遇到一个客户在他们的生产服务器上真正允许这样做)

但事实证明，让 SIF 为您记录一些东西其实非常简单。有两件事要做:

首先，您需要确保您已经注册了“`WriteInformation`”任务，这将启用日志记录。取决于你从哪个文件开始，这可能已经完成了——但是你需要的是确保任务被添加到“`/Register/Tasks`”块:

```
 "Register": {
        "Tasks": {
            "SetVariable": "Set-Variable",
            "WriteInformation": "Write-Information"
        }
    }, 
```

完成后，您现在可以添加自己的任务来输出您想要的任何生成的值。因此，在上面的“`/Tasks`”中的生成块之后，您可以添加:

```
 "Tasks": {

        /* Whatever generation tasks you need */

        "DisplaySqlCorePassword": {
            "Description": "Displays the Sql Core DB password.",
            "Type": "WriteInformation",
            "Params": {
                "MessageData": "[concat('Sql Core DB Password: ',variable('SqlCore.Password'))]",
                "InformationAction": "Continue"
            }
        }
    } 
```

以便获得为核心数据库生成的密码。您可以添加更多这样的密码来写出您需要重复使用的任何密码:

[![](img/100dfb4a9691c29515f8f863521d99a6.png)](https://jermdavis.files.wordpress.com/2019/04/sifloggeddata.png)

那只是绑定了一个基于“`WriteInformation`”的任务，输出的是“`MessageData`”定义的字符串。所以如果你愿意，你也可以构造更复杂的信息。

请记住，不要将这些值放在日志文件中…