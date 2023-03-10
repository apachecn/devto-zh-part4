# 使用 PowerShell 别名启动 GitKraken

> 原文：<https://dev.to/calvinallen/launch-gitkraken-using-a-powershell-alias-56h9>

毫无疑问，我非常喜欢 GitKraken(现存最好的 Git GUI)，对吗？

同时，我仍然喜欢命令行(此时我首选的控制台是 [PowerShell Core](https://github.com/PowerShell/PowerShell/releases/tag/v6.2.2) )。

我不断发现自己在命令行中，在我的本地存储库中做一些事情，并希望在不触摸鼠标的情况下启动 GitKraken。

因为我在 Windows 上， [GitKraken](https://www.gitkraken.com/git-client) 安装程序增加了一个右键上下文菜单选项，当在文件夹中右击时，“用 GitKraken 打开”: [![right-click context menu option](img/a08243e2f5c1cea44442a3654aac3932.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Rsv1GpI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.calvinallen.net/launch-gitkraken-using-a-powershell-alias/right-click-kraken.png)

我知道这样做的命令行参数是存在的——我只需要找到它们。启动`regedit`,搜索`gitkraken`,显示出我正在寻找的命令:

> `HKEY_CLASSES_ROOT\Directory\Background\shell\GitKraken\command`

我现在需要做的就是将它移植到 PowerShell 别名`gitkraken`(当然，您可以根据需要更改别名- `gk`，等等)。).

这是我最后得到的代码，添加到我的 PowerShell 配置文件中。请注意，我将 [GitKraken](https://www.gitkraken.com/git-client) 命令包装在一个函数中，然后使用别名，因为我很难将 GitKraken 命令直接绑定到别名中(可能只是我缺乏 PowerShell 技能)

```
Function Launch-GitKraken {
  Start-Process -FilePath "$home\AppData\Local\gitkraken\update.exe" -ArgumentList "--processStart=gitkraken.exe","--process-start-args=`"-p `"$(Get-Location)`"`""
}

Set-Alias -Name "gitkraken" -Value Launch-GitKraken -Force 
```

Enter fullscreen mode Exit fullscreen mode

将上面的代码块添加到您的 PowerShell 配置文件中-(传统或核心-两者都已经过测试并工作)。

此外，如果您使用 [VSCode](https://code.visualstudio.com/) ，并将您的终端设置为 PowerShell，您也可以从那里启动它，因为这也会获取您的配置文件配置——这是一个动态 gif 图，展示了这一过程:(inception！)

[![POSH Kraken in Action](img/178fe771487c84a1eb96f8c0f74a558b.png)](//posh-kraken.gif)

[如果你想试试 GitKraken，帮我个忙，用我的推荐代码报名试用::](https://luv2.dev/gitkraken)