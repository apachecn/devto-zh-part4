# 每天做好事第六天

> 原文：<https://dev.to/swlkr/do-good-daily-day-6-21ne>

📅2019 年 9 月 17 日
🔥六日连胜
📱每日行善
💰0.99 美元的价格
🤑0 销售
⌚️花了 5.5 个小时
💻37 个文件被修改 554 个添加(+) 81 个删除(-)
✅今天的目标:完成粗略版本的介绍视图

## SwiftUI 比 web 开发好

当它起作用时，它真的起作用了。今天预览功能运行良好，我设法通过简单的按钮轻松浏览了几个静态屏幕。如果你能称之为代码的话，那么让一个看起来非常定制化的东西在每个屏幕上只需要大约 70 行代码，这简直是疯了。这确实让我想知道我们在 web 开发领域到底在做什么。无论如何，我确实学到了一些 SwiftUI 技巧，我想我应该分享一下

## SwiftUI 全屏背景图

```
var body: some View {
        VStack {
        }
        .background(
            Image("bg")
            .resizable()
            .scaledToFill()
            .edgesIgnoringSafeArea(.all)
        )
} 
```

Enter fullscreen mode Exit fullscreen mode

这就够了。要么这样，要么一个`ZStack`并把图像作为第一个孩子。

## SwiftUI 居中一幅图像

或者其他任何东西。有趣的是，我在这个问题上折腾了一段时间，但我想到了一个相当不错的东西，假设你只有一件事需要关注

```
var body: some View {
        VStack {
             Spacer()

             Image("bg")

             Spacer()
        }

} 
```

Enter fullscreen mode Exit fullscreen mode

还有更多的代码和一些好东西，但总会有明天。