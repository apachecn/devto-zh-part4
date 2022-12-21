# NativeScript Fetch 到 HttpClient 的转换和捕获

> 原文：<https://dev.to/digitalix/nativescript-fetch-to-httpclient-conversion-and-gotchas-2jpm>

将 fetch 转换为 HttpClient 来进行 API 调用非常简单。但是有时做得不正确，尤其是在滥用异步管道的时候。

使用 HttpClient 而不是存在于浏览器和 NativeScript 应用程序中的老式全局获取 API 可能是有益的。当使用 NativeScript 构建 Angular 应用程序时，使用 HttpClient 有一些额外的好处，比如能够使用 Http 拦截器以及默认情况下获得一个 RxJS 可观察结果。但是在从使用 fetch 转换到使用 HttpClient 时，需要注意一些事情。

在这个由两部分组成的视频系列的第二部分中，我首先向您展示如何通过 Angular 应用程序在 NativeScript 中执行从 fetch API 到使用 HttpClient 的转换。然后我给你看一些你可以面对的，表面上可能不那么明显的麻烦。当然，我会告诉你如何消除这些麻烦。

这个视频建立在我们已经在第一部分中所做的基础之上:[本地脚本应用的简单开发后端](https://dev.to/digitalix/easy-dev-backend-for-nativescript-apps-1jm0)。

[https://www.youtube.com/embed/sFOUij61TvI](https://www.youtube.com/embed/sFOUij61TvI)

你可以在我的 YouTube 频道找到更多的 NativeScript 教程:[https://www.youtube.com/channel/UCajiMK_CY9icRhLepS8_3ug](https://www.youtube.com/channel/UCajiMK_CY9icRhLepS8_3ug)

你可以在我们位于[NativeScripting.com](https://nativescripting.com)的课程区找到更多关于 NativeScript 的内容。新的[UI 实践课程](https://nativescripting.com/course/nativescript-hands-on-ui)对于那些开始学习 NativeScript 和如何使用 UI 的人来说是一个非常受欢迎的起点。

让我知道你是否喜欢 Twitter 上的这个简短教程: [@digitalix](https://twitter.com/digitalix) 或者在这里评论。请将您的原生脚本相关问题发送给我，我可以用视频形式回答。如果我选择你的问题做视频回答，我会送你奖品。使用#iscriptnative 标签。