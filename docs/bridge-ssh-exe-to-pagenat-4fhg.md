# 从 ssh.exe 到选美的桥梁

> 原文：<https://dev.to/zobo/bridge-ssh-exe-to-pagenat-4fhg>

我做了很多 PHP 工作，并且是远程调试(XDebug)的早期采用者，但是最近几年我的工作安排不允许我这么做。然后，我想尝试 Visual Studio 代码远程开发，这似乎正是我一直在寻找的东西，然而在开始时有一个大的红色停止灯:

`Note: PuTTY is not supported on Windows since the ssh command must be in the path.`

这不可能！我对 SSH 协议了如指掌，不会让这个阻止我。

通宵工作的最终结果是这个脚本，它允许我使用存储在 Pageant 中的现有 ssh 密钥，并尝试 VS Code Remote...

[https://github.com/zobo/weasel-pagent-win32](https://github.com/zobo/weasel-pagent-win32)