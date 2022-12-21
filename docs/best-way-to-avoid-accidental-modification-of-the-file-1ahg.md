# 避免意外修改文件的最佳方式

> 原文：<https://dev.to/chanduthedev/best-way-to-avoid-accidental-modification-of-the-file-1ahg>

最近，我学到了一个非常有用的技巧来避免不小心修改/编辑一个原始文件。这将适用于所有 unix 类系统，包括 Mac。

想法是使用 chmod 命令将文件权限改为只读，如下图

```
 >chmod 444 file_name 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
 >chmod 400 file_name 
```

Enter fullscreen mode Exit fullscreen mode