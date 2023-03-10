# 如何用 Visual Studio 代码自定义新的 Windows 终端

> 原文：<https://dev.to/kasuken/how-to-customize-the-new-windows-terminal-with-visual-studio-code-56b1>

几天前，微软发布了新的 [Windows 终端](https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701)的早期版本。
Windows 终端是一个新的、现代的、快速的、高效的、功能强大的、多产的终端应用程序，适用于命令行工具和 Shell(如命令提示符、PowerShell 和 WSL)的用户。
它的主要功能包括多个选项卡、Unicode 和 UTF-8 字符支持、GPU 加速文本渲染引擎以及自定义主题、样式和配置。

很明显，这是一个托管在 GitHub 上的开源项目:[https://github.com/microsoft/terminal](https://github.com/microsoft/terminal)
随意参与。

目前它有点不稳定，将来会有更多的功能，但我会用几天，我喜欢这个想法。到目前为止，我一直在使用 [Cmder](https://cmder.net/) ，但是 Windows 终端有一些很有前途的新功能。

## 如何安装

您可以直接从 Windows 应用商店安装 Windows 终端，或者如果您想了解它是如何工作的，您可以下载源代码，构建它并启动终端。

[![](img/c3c833762108446569b41324e23452f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wqApQWKK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rb6x6yo858b6ufdt42vw.PNG)

商店版本的链接:[https://www . Microsoft . com/en-us/p/windows-terminal-preview/9n 0 dx 20 hk 701](https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701)
GitHub 项目的链接:[https://github.com/microsoft/terminal](https://github.com/microsoft/terminal)T5】源代码和项目确实有据可查。

## 第一次发射

如果你从开始菜单启动应用程序，它看起来像下面的截图。

[![](img/d8c3ced609fc8c1f657af383521628d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YgNGvWpc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iut0oi8j4iufgdyi6du6.PNG)

默认终端是 PowerShell。

如果您单击“+”符号，应用程序将在新标签中启动另一个终端。
如果你点击向下箭头符号⬇，你可以从列表中选择新的终端。

列表下方还有一个有趣的按钮，设置按钮。

## 设置

如果你点击向下箭头，然后点击“设置”按钮，此时，设置文件(它是一个 JSON 文件)将在一个新的 Visual Studio 实例中打开。
如果你愿意，而是直接打开文件，可以打开 Visual Studio 代码，直接从目录:**% user profile % \ AppData \ Local \ Packages \ Microsoft 打开文件。windows terminal _ 8 wekyb 3d 8 bbwe \ local state**

在这个文件中，您可以找到 Windows 终端的所有设置。
目前，您只能通过该文件与设置进行交互，但在将来，UI 将会添加到应用程序中。

## 添加新的个人资料

如果您想添加一个新的概要文件，请转到 JSON 设置文件的“概要文件”部分，并添加一个新的概要文件部分，如下所示:

```
 {
        "acrylicOpacity" : 0.85,
        "background" : "#012456",
        "backgroundImage" : "C:/users/barto/AppData/Local/Packages/Microsoft.WindowsTerminal_8wekyb3d8bbwe/RoamingState/unicorn.gif",
        "backgroundImageOpacity" : 0.7,
        "backgroundImageStretchMode" : "uniformToFill",
        "closeOnExit" : false,
        "colorScheme" : "Solarized Dark",
        "commandline" : "powershell.exe",
        "cursorColor" : "#00FF00",
        "cursorHeight" : 25,
        "cursorShape" : "vintage",
        "fontFace" : "Fira Code",
        "fontSize" : 12,
        "guid" : "{79285a8e-036c-446f-8a9c-78994e34bf78}",
        "historySize" : 9001,
        "icon" : "ms-appdata:///roaming/pwsh-32.png",
        "name" : "PowerShell with Unicorn",
        "padding" : "0, 0, 0, 0",
        "snapOnInput" : true,
        "startingDirectory" : "%USERPROFILE%",
        "useAcrylic" : false
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果您想从这里复制这一部分，请注意 guid。记得每次粘贴文件时都要修改它。
例如用于设置默认终端，在文件中必须唯一。

正如你在我的个人资料部分看到的，你可以添加图片作为背景，改变字体大小，光标形状等等。例如，我使用 Fira 代码作为字体，因为在 Visual Studio 代码中我也喜欢它。
在文中增加了一些牛逼的体验。
可以从这里下载:[https://github.com/tonsky/FiraCode](https://github.com/tonsky/FiraCode)

如果你想在会议上给你的朋友或与会者留下深刻印象，你可以添加一个 gif 作为背景。

你可以在下图中看到它的作用。

[![](img/37ba7ce841776d9e10101ff018a68d9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nN8gXJy---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uaackzt4yxp854i0l5rl.gif)

## 配色方案

在 profiles.json 文件的末尾，您可以找到一个名为“schemes”的部分。从这里您可以添加或更改终端的默认颜色。
如果您想在您的新配置文件中使用它，您必须在属性“name”和配置文件部分的“colorScheme”属性中插入相同的方案名称。

## 分享您的个人资料

我在我的个人资料中创建了一个新的 gist 来分享我的个人资料设置，你可以在这里找到它:[https://gist . github . com/ka suken/076d 68 b 92 e 2 a 67 dfda 591587 c 77 a 40 c 0 # file-profiles-JSON](https://gist.github.com/kasuken/076d68b92e2a67dfda591587c77a40c0#file-profiles-json)。
在评论中分享你的个人资料！
我们可以一起创造出令人惊叹的东西！

[![](img/e08f3bfd009d00d8c5a931f4fae4fdf4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q7uZyp2O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2k9fw1y21k3sv8q1swr3.gif)

### 我的 gif

一些用户要求 gif。
下面我分享一下！！！

[![](img/4b929bef6c7c68174a418b33eb167300.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kTsDcTzl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s8ysw6hfoj1a61ovz0us.gif)

[![](img/6db58ac0cd770f4b54c3ad5e76ff3cfb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LBM53ShP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3u7x3b3otmyh6kytychp.gif)

[![](img/9aa4c9418cab3db812ccd663535d8a15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TAwcgTYn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/asvdrzeanv9a20jekihr.gif)