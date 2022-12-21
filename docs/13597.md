# 构建完全可扩展的开发者协作平台

> 原文：<https://dev.to/lostintangent/building-a-collaborative-development-platform-5c34>

*注意:我是 [Visual Studio Live Share](https://aka.ms/vsls) 团队的成员，这篇文章是解释我们为什么开发这个产品以及我们正在探索的想法的系列文章的一部分。*

现实世界的开发比仅仅编写代码更复杂，这就是为什么协作工具需要超越基本的实时编辑器。当我们构建 [Visual Studio Live Share](https://aka.ms/vsls) 时，我们希望支持*端到端开发人员协作*，这不仅提供实时编辑，还提供远程语言支持(自动完成、转到定义等)。)、[项目范围的导航/搜索](https://dev.to/lostintangent/collaboration-doesn-t-come-in-one-size-fits-all-33ai)、共享构建、调试、终端、本地主机服务器、[源代码控制](https://aka.ms/vsls)、[集成聊天&代码注释](https://aka.ms/vsls-chat)等等。这种*协作广度*使得 Live Share 能够用于如此多样的[用例](https://aka.ms/vsls-usecases)(结对编程、合并冲突、现场诊断、黑客马拉松、教育等)。)，是屏幕共享的可行替代方案。事实上，如果您想知道 Visual Studio 代码的哪些部分是在实时共享会话期间与来宾共享的，下面的屏幕截图应该有助于说明这一点(提示:它是一切🚀).

[![E2E](img/b85a3241cb9e1a37079cec1f754d8ba3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wNjmwIXA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/116461/60688281-173ae080-9e69-11e9-8147-4136a75c190b.png)

开箱即用的实时共享通过协作的超级能力增强了 Visual Studio 代码的每个功能，同时允许每个人*保留他们的个性化环境*(例如主题、按键绑定)。这样，开发人员可以利用他们喜欢的工作流和 IDE 配置，无论他们是单独工作还是一起工作。唯一的区别是你是否点击了“分享”按钮👩‍💻

虽然 Visual Studio 代码本身提供了一组引人注目的功能(例如集成调试、版本控制)，但它最大的优势之一在于其第三方扩展的[生态系统，其中许多是开发人员工作流程的基础(例如](https://marketplace.visualstudio.com/vscode) [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) 、 [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) )。因此，Live Share 实际上无法实现其承诺的*端到端协作*，除非它也为扩展提供一种机制来实现协作，并为体验贡献其独特的功能。所以我们别无选择，只能构建一个 [Live Share SDK](https://npmjs.com/vsls) 🔥

## 一个协作的生态系统

在高层次上，Live Share 通过建立 E2E 加密的对等网络来工作，然后使用一组内置的 [RPC 服务](https://en.m.wikipedia.org/wiki/Remote_procedure_call)来公开来自“主机”的远程操作(例如，转到定义，调试程序)，并在参与者之间实时同步编辑器状态(例如，文本编辑，光标位置，终端输出)。Live Share 提供了足够多的服务，使核心 Visual Studio 代码体验完全协作化(例如调试、编辑、终端)，但通过 [Live Share SDK](https://npmjs.com/vsls) ，它还支持扩展贡献自定义服务，然后可以在相同的安全通道上交换消息。此外，Live Share SDK 使扩展能够为其“会话详细信息”视图提供自定义 UI，这支持改进的可发现性和与核心 Live Share 功能集的集成。

<figure>

[![Counter](img/8c75812335ca2b14bee41dd1ca4aac5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ciXltEhC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/116461/59561482-b771b800-8fd5-11e9-9bcb-7b77ce0207f6.gif)

<figcaption>A sample collaborative counter, exposed via a custom Live Share service</figcaption>

</figure>

有了这个，Live Share 既可以作为一个你可以使用的产品，也可以作为一个你可以扩展的**开发者协作平台**，它可以实现一些真正引人注目的场景！例如:

*   [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) 为“访客”公开一个实时共享服务，让他们远程浏览项目的 Git 提交历史，就好像它是本地可用的一样([示例](https://twitter.com/LostInTangent/status/1085919402954874883)

*   测试浏览器公开了一个服务，用于查看和执行单元测试，并同步它们的通过/失败状态

*   [浏览器预览](https://marketplace.visualstudio.com/items?itemName=auchenberg.vscode-browser-preview)公开了一种用于协作浏览网页，以及同步鼠标和键盘输入的服务。此外，它将“共享浏览器”的概念添加到实时共享树视图中([示例](https://twitter.com/auchenberg/status/1116362646784102400)

*   [Team Chat](https://marketplace.visualstudio.com/items?itemName=karigari.chat) 公开了一个发送和接收文本聊天消息的服务。此外，它向实时共享树视图添加了一个“聊天频道”入口点([示例](https://twitter.com/lostintangent/status/1075155769870307329)

*   [白板](https://aka.ms/vsls-whiteboard)公开了一项服务，用于协作绘图、同步形状和编辑操作(例如撤销、更改背景颜色)([示例](https://twitter.com/lostintangent/status/1079846355290415104)

*   [Pomodoro](https://aka.ms/vsls-pomodoro) 公开了一个协作计时器的服务，并同步停止和启动它。计时器本身在实时共享树视图中显示为一个节点([示例](https://twitter.com/lostintangent/status/1115847842453762049)

拥有这些类型的集成使 Live Share 本身保持精简，同时，使扩展能够通过新的有趣的“点亮”体验(例如，浏览文档、白板、协作 TDD)来增强它。此外，它允许我们与社区合作，并探索开发人员协作如何随着时间的推移继续发展和成熟。

除了构建定制服务，扩展还可以订阅特定于实时共享的事件，并在最终用户处于协作会话中时增强其行为。这样，Live Share 代表了一种扩展可以使用的“模式”,因此协作成为了您工作方式的一个基本方面。例如:

*   [Peacock](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock) 允许您在主持实时共享会话时指定自定义的工作台颜色，以便在视觉上清楚地看到您正在共享

*   [实时服务器](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)检测到您正在进行实时共享会话，并自动与客人共享其 HTTP 服务器([示例](https://twitter.com/LostInTangent/status/1063445225140568065)

*   [Code Time](https://marketplace.visualstudio.com/items?itemName=softwaredotcom.swdc-vscode) 自动跟踪与他人合作所花费的时间，作为你的总体时间报告中的一个关键指标([示例](https://twitter.com/brettmstevens/status/1103809321702313984)

*   [配置文件切换器](https://marketplace.visualstudio.com/items?itemName=aaronpowell.vscode-profile-switcher)允许您为实时共享设置自定义配置文件，这样您就可以轻松关闭您的搭档可能不喜欢的设置(例如保存时的格式)([示例](https://user-images.githubusercontent.com/116461/60541846-4dd8f580-9cc7-11e9-8b57-b8aca0252fb4.gif)

## 分享生活中所有的事情

在任何时候，你都可以通过查看[Awesome Live Share](//aka.ms/vsls-awesome)GitHub repo 来查看增强 Live Share 的扩展列表，并留意自述文件上带有“Live Share Enabled”标记的扩展。此外，您还可以在[Live Share Enabled Extensions](https://trello.com/b/CLfMhSgq/live-share-enabled-extensions)Trello 板上跟踪和投票支持计划中和正在进行的集成。我们还有很多工作要做，我们需要反馈来确定我们工作的优先顺序👍

<figure>

[![Badge](img/d0022d1a1f026af6eb37c50aec28b2c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yVbhWUwb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aka.ms/vsls-badge)

<figcaption>The sign of a truly awesome VS Code extension</figcaption>

</figure>

如果你已经构建了一个 Visual Studio 代码扩展，并认为它将从实时共享集成中受益，那么请查看 [SDK 的文档](https://npmjs.com/vsls)，以及简单的[计数器示例](https://github.com/vsls-contrib/counter)以便开始。此外，联系我讨论将扩展添加到我们的允许列表，因为只有批准的扩展可以使用 Live Share RPC 通道(出于隐私和安全目的)。我们非常高兴能继续与社区合作，并学习新的方法使开发者协作更加愉快！🙌