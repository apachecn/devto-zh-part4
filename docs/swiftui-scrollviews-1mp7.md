# SwiftUI 滚动视图

> 原文：<https://dev.to/leejarvis/swiftui-scrollviews-1mp7>

原帖:[https://leejarvis.me/posts/2019/swiftui-scrollviews](https://leejarvis.me/posts/2019/swiftui-scrollviews)

我最近写了我的 [WWDC 集锦](https://leejarvis.me/posts/2019/wwdc19)，以及 SwiftUI 对我来说是一个巨大的惊喜。我决定做一部戏，造一些小东西。

几年前，我开发了一个 iOS 应用程序，可以追踪家人和朋友的礼物想法。这不是我曾经发布到 app store 的东西，但我确实保留它供个人使用。

这个应用程序并不特别令人兴奋。它存储礼物的想法，并跟踪生日，周年纪念日等。在应用程序的主屏幕上，我有一个
水平滚动视图，显示我即将进行的一些活动；“妈妈的生日”，“我的结婚纪念日”。

这些活动“卡片”由一个名字和一个自定义表情符号/背景颜色组成。为了彻底简化，它们看起来像这样(但是更好，我保证):

[![](img/3f363849514ee848145442f8f7cac3e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YnJFQtw5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lga5h23usggenmcqha3c.png)

ScrollView 并不特别难构建，但是让 AutoLayout 在任何方向的所有设备上都能很好地运行，这并不是我的乐趣所在。这不仅乏味而且耗时，还真的吸干了创建应用程序的乐趣。

使用 SwiftUI，我能够用几行代码重新创建一个类似的 scroll view:

```
import SwiftUI

struct Event: Identifiable {
    let id: Int
    let name: String
    let emoji: String
    let color: Color
}

let events = [
    Event(id: 0, name: "Jon's Birthday", emoji: "🥳", color: Color.red),
    Event(id: 1, name: "Wedding", emoji: "👰", color: Color.blue),
    Event(id: 2, name: "Aimee's Birthday", emoji: "🎉", color: Color.green),
    Event(id: 3, name: "Christmas", emoji: "🎄", color: Color.purple),
]

struct ContentView : View {
    var body: some View {
        ScrollView {
            HStack {
                ForEach(events) { event in
                    VStack {
                        Text(event.emoji)
                            .font(.system(size: 50))
                        Text(event.name)
                            .font(.system(.caption))
                    }
                    .padding(40)
                    .background(event.color)
                    .cornerRadius(10)
                }
            }
        }
        .padding()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是整个文件。继续，粘贴到 Xcode 11 beta。

没有自动布局的限制，没有故事板，没有对每台设备进行测试，只是为了确保我没有破坏什么东西。UI 就在代码中。

声明式语法确实使代码易于阅读和编写，
使用 Xcode 强大的内置文档和建议工具，
我发现自己创建用户界面是一种非常自然的方式。

这是基于 macOS Mojave 开发的，它不能访问实时
预览画布和拖放行为。这意味着我每次都必须重新构建
应用程序来预览我的更改。不麻烦，我已经做那个
很多年了。我只能想象这在 macOS Catalina 上要好得多。

我也看了一些 WWDC 会议的视频。SwiftUI 上有一些不错的:

*   [swift ui 简介:构建您的第一个应用](https://developer.apple.com/videos/play/wwdc2019/204/)
*   swift ui essentials
*   [swift ui 中的可访问性](https://developer.apple.com/videos/play/wwdc2019/238/)

所有 WWDC 的视频都可以在这里找到。

我将继续玩 SwiftUI，并可能在未来几周写一两篇文章。我对这项技术感到无比兴奋；它使得构建移动应用程序变得更加容易。最后，对我来说，iOS
开发又有乐趣了。