# 用 powershell 撞击 nuspec 文件版本

> 原文：<https://dev.to/sheldonhull/bump-nuspec-file-version-with-powershell-3f00>

## 凹凸 Nuspec 版本

修改 nuspec 文件的版本需要一些调整，我从 slack powershell 社区获得了一些帮助，以确保我正确地处理了 xml 解析。这就是结果。如果您正在通过代理运行 chocolatey 包构建或等效的 nuspec 构建，并且想要一种方法来确保最新的构建以增量方式更新构建版本，这应该会有所帮助。

这个代码片段应该有助于您以编程方式修改 nuspec 文件版本。