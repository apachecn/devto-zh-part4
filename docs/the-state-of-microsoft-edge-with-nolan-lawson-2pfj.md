# 微软与诺兰·劳森的较量

> 原文：<https://dev.to/thisdotmedia/the-state-of-microsoft-edge-with-nolan-lawson-2pfj>

[https://www.youtube.com/embed/_sUMIqKtf-c](https://www.youtube.com/embed/_sUMIqKtf-c)

微软 Edge 是 Windows 10 新的默认和推荐浏览器。微软 Edge 从 2014 年开始生产，即将发布第四个版本 Edge 15。

虽然 Internet Explorer 11 仍然可用，但它将是最后一个版本。

# 新发布

Windows 有一个发布周期，要求每年发布两次可预测的版本。在 Windows Insider 程序中有这些版本的预览，每个人都可以访问，包括没有@microsoft.com 帐户的人。有了这个程序，个人有机会检查新版本的浏览器，并获得经常更新，通常是一周一次或每两周一次。

# 没有窗户？没问题！测试边缘

为了测试 Edge，与 BrowserStack 云测试建立了合作关系。此选项可用，因此不使用 Windows 的个人也可以尝试最新版本的 Edge。

# 互通&基础知识

Microsoft Edge 从一开始就关注互操作性和基础。为了确保在 HTML5 测试中的良好性能，以及与 Chrome 57、Firefox 52、Safari 10.1 和 IE11 的性能比较，在浏览器上做了大量工作。

# 边缘的新特性 15

Edge 15 拥有许多令人兴奋的新功能，包括所有 ES2016 功能、WebVR、WebAssembly 和缓存控制。您可以转到 about:flags 来尝试一些实验性的特性，比如 service workers 实现和 SharedArrayBuffer。

# 2017 年，继续前进

微软 Edge 的未来计划包括对
服务工作者和相关 API(后台同步、推送 API 等)的改进。)
-DOM 性能，并确保 Edge 在现代 web 中运行良好
-开发工具，更具体地说是 f12 工具
-PWA 和实现
-CSS 现代化，包括对象适配、对象位置、网格

Edge 的进步是通过更开放的方法实现的。Windows 开发人员正在与社区分享他们的进展，以避免实现人们不想使用的 API。浏览器本身并不是开源的，但是许多组件都可以贡献出来。例如，ChakraCore、Edge、WebGL renderer、Browser Efficiency Test 和 Elevator 都是向公众公开的组件列表，可以在[github.com/microsoftedge](http://github.com/microsoftedge)上访问。

目前正在建设中的 API 在[status.microsoftedge.com](//status.microsoftedge.com)上展示。通过访问用户声音的链接，用户可以对 API 项目的优先级进行投票。这种反馈有助于衡量 web 开发人员和设计人员对不同 API 的热情程度。
除了这个功能，当问题被披露时，也可以建议如何修复浏览器中的错误。

个人可以访问[issues.microsoftedge.com](//issues.microsoftedge.com)查看所有未解决的问题，跟踪问题，查看哪些问题分配给了谁，并确定是否发布了修复程序。任何问题都会被归档到一个 bug 追踪器中，然后由员工进行分类。

为了使这个过程更加容易，贡献者可以简单地在 twitter 上添加#edgebug 标签，并包含一个到他们的 JSbin、codepen 等的链接。这些帖子与其他问题跟踪者进入同一管道，也将被审查。

要与 Edge 保持联系或了解新功能，请查看他们在 blog.microsoftedge.com 的博客或在 Twitter @MSEdgeDev 上关注他们。

作者 trinh kien 和领口 hubner

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。