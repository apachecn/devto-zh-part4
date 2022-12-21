# go.mod replace: fallthrough

> 原文：<https://dev.to/nanohard/go-mod-replace-fallthrough-2k8p>

### 当你想替换一个导入的 pkg 使用的 pkg 时，如何使用`go.mod`中的`replace`？

## 问题

**项目**使用**库**
库**库**使用**子库**T9】

```
Project
  Lib
    SubLib 
```

Enter fullscreen mode Exit fullscreen mode

您想用您正在处理的本地版本替换**子库**，以便在**项目**中看到结果。

## 答案

打开**项目**中的`go.mod`来使用`replace`指令。

```
// go.mod

require(
  github.com/example/lib
  // `lib` imports github.com/sublib/sublib
)

replace github.com/sublib/sublib => /local/dir/sublib 
```

Enter fullscreen mode Exit fullscreen mode

利润！

愿我痛苦的时光能帮助他人。