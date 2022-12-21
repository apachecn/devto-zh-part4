# 艾迪·奥斯马尼的状态

> 原文：<https://dev.to/thisdotmedia/the-state-of-chrome-with-addy-osmani-14ib>

[https://www.youtube.com/embed/bz4tfJqYvuA](https://www.youtube.com/embed/bz4tfJqYvuA)

渐进式网络应用程序(PWAs)正在改变用户在移动设备上与网络互动的方式。PWAs 为移动用户提供了快速、可靠和有意义的服务。最近，新兴市场对 pwa 的采用有所增加，突出了 pwa 必须提供的性能优势和新功能。

# 改进

为了让 PWAs 更深入地与 Android 集成，Lighthouse 被创建为一个清单，以帮助指导功能更易于管理，就像一个原生应用程序一样。当这些功能符合 Lighthouse 中测试的要求时，pwa 就可以在 app store 中与原生应用并列列出。

除此之外，现在还有一个选项，包括推送通知和航运导航器媒体会话，以及支持全屏显示，让开发商完全控制手机上的所有房地产。

# 新增功能

Chrome 上即将发布许多新版本，值得期待。即将推出的 web share API 正在 Twitter 上进行 is origin 测试，将减少用户间共享所需的大量基础工作。

Chrome 57 上提供了形状检测 API。它利用移动设备内的硬件加速，简化了形状、条形码和物品的检测。

Chrome 还引入并打算推出图像捕捉 API，让用户能够控制相机选项，如照片、控制变焦、对焦和选择分辨率。

Chrome 53 中提供的支付请求 API 提供了一个原生用户界面，使结账流程变得更加容易。个人以快速和一致的方式支付选择和添加付款。

# 性能提升

最初，V8 在强调峰值性能方面是成功的。然而，优化并不总是现实世界 web 应用的代表。通过使用速度计(对框架代码进行压力测试的基准)，团队能够针对现实工作负载调整他们的优化。通过评估流行网站的优化，并在解析和编译上花费更多的时间，他们能够看到性能的实质性提高。新的开发工具

一些新的 Chrome 开发工具包括代码覆盖、请求阻塞和审计 2.0。

canary 中提供了代码覆盖特性，它允许用户记录一个页面，确定执行什么 javascript，使用或不使用多少 JavaScript，并处理代码路径。

包括时间点记录，以允许在不同视图间导航。CSS 统计数据也根据应用程序被使用的时间而给出。

请求阻止选择并阻止请求 URL 或请求域。
Audit 2.0 类似于开发工具中的灯塔。运行审计时，您可以运行 Lighthouse 中提供的所有 PWA 和 web 平台最佳实践测试，并运行报告。

你可以在[developers.google.com/web](//developers.google.com/web)了解最新的功能，并在推特 [@addyosmani](https://dev.to/addyosmani) 上关注 Addy Osmani。

作者 Trinh Kien & Necoline Hubner

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。