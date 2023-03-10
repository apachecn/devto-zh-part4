# 如何以一种简单的方式改变 VSCode 中的终端样式

> 原文：<https://dev.to/sfarias051/how-to-change-the-terminal-style-in-vscode-in-an-easy-way-5139>

我只需要改变集成终端的外观，我就知道这有多简单。

所以…在这里你可以愉快地四处走走🙂

1.  转到设置(显然是在 VSCode 中)
    *   在 Windows/Linux 上- **文件>首选项>设置**
    *   在 macOS - **上编码>偏好设置>设置**
        *   快捷方式(⌘)
        *   搜索(⇧⌘P) →“首选项:打开设置”
2.  搜索“工作台:颜色定制”并打开 **settings.json** 文件
3.  在**work bench . color customizations**下编辑或粘贴您的配置
    1.  [https://code.visualstudio.com/api/references/theme-color](https://code.visualstudio.com/api/references/theme-color)

```
 "workbench.colorCustomizations":  {  "terminal.background":"#1D2021",  "terminal.foreground":"#A89984",  "terminalCursor.background":"#A89984",  "terminalCursor.foreground":"#A89984",  "terminal.ansiBlack":"#1D2021",  "terminal.ansiBlue":"#0D6678",  "terminal.ansiBrightBlack":"#665C54",  "terminal.ansiBrightBlue":"#0D6678",  "terminal.ansiBrightCyan":"#8BA59B",  "terminal.ansiBrightGreen":"#95C085",  "terminal.ansiBrightMagenta":"#8F4673",  "terminal.ansiBrightRed":"#FB543F",  "terminal.ansiBrightWhite":"#FDF4C1",  "terminal.ansiBrightYellow":"#FAC03B",  "terminal.ansiCyan":"#8BA59B",  "terminal.ansiGreen":"#95C085",  "terminal.ansiMagenta":"#8F4673",  "terminal.ansiRed":"#FB543F",  "terminal.ansiWhite":"#A89984",  "terminal.ansiYellow":"#FAC03B"  } 
```

稍作改动后，您可以在 VS 代码
[![Integrated Terminal VSCode](img/a85317ba830474e8621522177b2868f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0fxUNxHN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paper-attachments.dropbox.com/s_BB4D438C0BD300F9046363852C7E5AEEFC644AC076498F4495212D2CD4CFEFF9_1565661101879_Ex.png) 中查看您的集成终端

你可以在这里找到几个预定义的模板:
[https://glitchbone.github.io/vscode-base16-term/#/](https://glitchbone.github.io/vscode-base16-term/#/)

> [https://code.visualstudio.com/docs/getstarted/settings](https://code.visualstudio.com/docs/getstarted/settings)
> 
> [https://code.visualstudio.com/api/references/theme-color](https://code.visualstudio.com/api/references/theme-color)
> 
> [https://glitchbone.github.io/vscode-base16-term/#/](https://glitchbone.github.io/vscode-base16-term/#/)