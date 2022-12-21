# 如何在 iOS 的生产版本中调试

> 原文：<https://dev.to/nil4uyou/how-to-debug-in-production-build-of-ios-32an>

这里有一篇小文章是写给那些在生产环境中而不是在开发环境中面临 bug 的开发人员的。

最近我在做 ionic 项目的时候也遇到了一个问题。我不确定这个问题是由于谷歌地图插件的错误还是由于其他开发者的代码改动。

我的想法是怀疑状态，我如何能在生产建设的 iOS 调试。事实是你不能，但在做一些搜索时，我发现了一个叫做(iOSConsole)的工具([http://lemonjar.com/iosconsole/](http://lemonjar.com/iosconsole/))，所以[这里是这个工具的链接](http://lemonjar.com/iosconsole/)。

开发者能做的就是放一些日志代码。(即 phonegap 或 ionic base 项目中的 console.log)并放置 try、catch 和 finally 语法。