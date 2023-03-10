# 我能给另一个开发者的最大建议

> 原文：<https://dev.to/dvddpl/the-biggest-advice-i-could-give-to-another-developer-3jme>

> 调试很难。从事全栈项目更是难上加难。

如果您正在调查您的 **React** 单页面应用程序中的一个错误，该应用程序将数据发送到 **AWS** 上的无服务器后端，将数据保存到数据库，然后刷新视图，在您的架构中有许多移动的部分，许多不同的组件。甚至重现 bug 并确定**bug 在哪里**也是一项令人生畏的任务。

**我在编写**(无论是实现新功能，还是修复错误)代码时遵循的咒语，也是我在帮助初级(或不太初级)开发人员时经常重复的咒语:

> ### Narrow the scope and speed up the feedback loop

这是什么意思？

### 缩小范围

[![zoom in and reduce scope](img/9f748bd9f8bc3773730b642c6324b90e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2oFm6Io---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/err4v32owcs5kg607p08.png)

*   把问题分成更小的问题
*   关注单个的小细节
*   尽可能在最小的代码段上操作，并且直接操作。

当然，您需要花一些时间来重现和研究整个应用程序的怪异行为。这很难，但也很有趣——这是一种调查任务。
但是一旦你发现哪段代码是负责任的——或者即使你只是有一些怀疑，只要专注于那一部分。忘记其他的。

你需要加快你的生产力的是缩小范围。
**清除杂物**。

关闭 react 应用程序的本地服务器，不要再仔细检查 DBeaver 上的表。不要再想在 AWS 控制台中检查日志了。
**你知道——或者你应该定义——你期望的输入是什么，你想要的输出是什么。**写一个单元测试来证明这一点。其他的都不重要了。

节省时间，点击和理智，只关注那些导致错误的代码。

### 加速反馈循环

[![increase the speed of the feedback loop](img/6bb2b9859deca75d0baca3424fbaf463.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U_F6Cmcz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d7hl87rwklr4lm14lw6h.jpg)

简单地说，反馈循环就是**让你明白你刚刚改变的东西是否有效的循环**。

*   重现 bug
*   修改一些代码
*   检查 bug 是否还在
*   再改一些代码
*   迭代直到 bug 消失

在这样做的时候，你不希望花费大部分时间等待代码编译/热加载，你肯定不希望浪费时间在你的应用程序中点击，用假数据填充表单(并在你的后端产生更多垃圾)，你不希望无聊地等待服务器响应。

你的反馈回路越短越快，你就越有效率。你在编码方面学得越多/变得越熟练——因为你花更多的时间写代码，而不是等待反馈循环或试图重现导致错误的所有步骤。

不要不停地重新加载应用程序，去打破形式，填充它，等待后端的响应，然后读取所有地方的日志...

正如我所说的，你知道——或者你应该定义——你期望的输入和你想要的输出。

如果可能的话，编写一个单元测试，或者如果必要的话，编写一个集成测试(如果你真的需要与数据库交互的话)
打开你的 IDE，在调试模式下运行代码，设置断点并检查正在发生的事情。

(请不要依赖到处散布的控制台.日志！)
[![console.logs everywhere](img/bd592da035dbd0ea9208b994f7b473b5.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--rIyO5yGQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qxpgghpvutlp33s2jqfr.jpg)

进行一些更改，然后再次运行测试。有时甚至不必是单元测试，一个简单的 js 文件调用你的方法，你直接用 node 执行(总是打开调试器并设置断点)就足够了。
*“但是..”*，你可能会说，*“数据从前端传入 lambda，如果我不每次都填写表单，我就不知道传入的数据是什么。”*
嗯。然后手动执行最后一次操作，在网络控制台中检查正在发送什么，或者在 Lambda 日志中检查正在接收什么(网关 API 总是用一些其他东西包装数据)，将数据保存到一个文件中，或者在测试中硬编码，然后直接将其提供给行为异常的方法。

概括一下:

*   努力缩小你的问题范围，
*   关注更小的部分
*   找到每一个快捷方式，让您快速重现您的错误，并立即测试您的更改
*   收紧反馈回路

调试愉快！

阿列克谢·斯库塔里、[保罗·斯科鲁普斯卡斯](https://unsplash.com/@pawelskor?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)和[陈以桐](https://unsplash.com/@ja5on?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash 上的照片