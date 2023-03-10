# 在某些 macOS 应用程序中启用黑暗模式

> 原文：<https://dev.to/equiman/enable-dark-mode-in-some-macos-apps-4c1f>

对我来说，macOS 的黑暗模式是一种欺骗。一旦你选择了黑暗的一面，要么全是黑暗，要么什么都没有...而且你不能在轻模式下选择你想要的应用。像 Mail 或 Finder 这样的应用在黑暗模式下最难看，也很难使用。

幸运的是，有一种方法可以让光线模式和使用终端中的一个命令只改变想要的应用程序为黑暗:

```
defaults write [Plist ID] NSRequiresAquaSystemAppearance -bool [Option] 
```

Enter fullscreen mode Exit fullscreen mode

用所需值替换`[Plist ID]`和`[Option]`。

## Plist 值:

| 身份证明 | 应用 |
| --- | --- |
| `-g` | 停靠和菜单 |
| `com.apple.notificationcenterui` | 通知中心 |
| `com.apple.Spotlight` | 聚光灯 |
| `com.apple.finder` | 探测器 |
| `com.apple.iChat` | 信息 |
| `com.apple.iCal` | 日历 |
| `com.apple.iTunes` | 苹果公司开发的热门软件（苹果公司开发的与 iPod 搭配的在电脑上使用的音乐播放器和音乐管理器） |
| `com.apple.Terminal` | 末端的 |
| `com.apple.ActivityMonitor` | 活动监视器 |
| `com.apple.AppStore` | 应用商店 |
| `com.apple.dt.Xcode` | Xcode |
| `com.bohemiancoding.sketch3` | 素描 |
| `com.apple.Safari` | 旅行队 |
| `com.google.Chrome` | 谷歌浏览器 |

你可以去`~/Library/Preferences`可以看到完整的 Plist 名字列表。

## 选项值:

| [计]选项 | 方式 |
| --- | --- |
| `FALSE` | 黑暗 |
| `TRUE` | 光 |

执行完命令后，重启应用程序。仅对于 Dock & Menu，您需要重新启动您的会话。

* * *

**都是乡亲们！**
**快乐编码** * 🖖

[![beer](img/192892baef71a32ea4a5e98a4927b05e.png)](https://github.com/sponsors/deinsoftware)