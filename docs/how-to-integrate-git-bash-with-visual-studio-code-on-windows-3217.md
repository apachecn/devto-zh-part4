# 如何在 Windows 上集成 Git Bash 和 Visual Studio 代码

> 原文：<https://dev.to/simbo1905/how-to-integrate-git-bash-with-visual-studio-code-on-windows-3217>

许多客户会要求我使用 Windows 笔记本电脑来访问他们的网络。幸运的是，我可以更改 VS 代码中的设置，将 Git Bash 用作内置终端。然后，我可以在 Windows 笔记本电脑上获得与在家里的 mac 电脑上相同的开发体验。

首先键入“Ctrl+Shift+P”打开命令搜索，然后键入/选择“打开用户设置”。如果这显示一个设置搜索页面，你需要点击右上角的“{}”进入原始 JSON。合并以下设置，确保使用与“bash.exe”和“git.exe”二进制文件安装位置相匹配的路径:

```
{  "terminal.integrated.shell.windows":  "C:\\whereever\\Git-2.17.1\\bin\\bash.exe",  "terminal.integrated.env.windows":  {  "CHERE_INVOKING":  "1"  },  "terminal.integrated.shellArgs.windows":  [  "-l"  ],  //  optional  add  git-path  to  use  this  version  of  git  for  built  in  visual  studio  model  "git.path":  "C:\\whereever\\Git-2.17.1\\bin\\git.exe"  } 
```

请注意，您不必使用最后一个设置，它告诉 VS 代码使用 git Bash 自带的 Git 来获得其内置的 Git 特性。

这种方法的优点在于，您不必切换到单独的 Bash 窗口。嵌入式终端在 VS 代码中运行，并在磁盘上正确的文件夹中启动。VS 代码将过滤终端输出，寻找像`./server.js`这样的文件路径，并将它们转换成超链接。ctrl+点击它们，它将在主编辑器面板中打开文件！当您在终端中运行的构建或单元测试抛出一个错误，并记录要打开的文件时，这非常有用。

Git Bash 提供了所有您喜欢的 coreutils 工具，比如 find、awk、sed、ssh 等等。您也可以使用`cat whatever | clip`将输出捕获到 windows 剪贴板。可以用`cat /dev/clipboard | awk -f mega_skills.awk`读取 windows 剪贴板。你可以彻底解决任何问题，并赢得一整天的胜利。

尽情享受吧！