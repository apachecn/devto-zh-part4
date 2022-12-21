# git 提示——在所有子目录下运行 git pull

> 原文：<https://dev.to/lqj/git-tips-run-git-pull-over-all-subdirectories-1im9>

我经常使用的非常方便的提示。

# Mac / Linux

```
ls | xargs -I{} git -C {} pull

// run in parallel
ls | xargs -P10 -I{} git -C {} pull 
```

Enter fullscreen mode Exit fullscreen mode

# 窗口

**旧 DOS 批处理文件:**

```
@echo off
for /f %%f in ('dir /ad /b') do (
 cd "%%f"
 git pull
 cd..
) 
```

Enter fullscreen mode Exit fullscreen mode

# 引用:

[https://stack overflow . com/questions/3497123/run-git-pull-over-all-subdirectories](https://stackoverflow.com/questions/3497123/run-git-pull-over-all-subdirectories)