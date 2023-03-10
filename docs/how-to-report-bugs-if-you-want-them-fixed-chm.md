# 如何报告错误(如果你想修复它们)

> 原文：<https://dev.to/makarov/how-to-report-bugs-if-you-want-them-fixed-chm>

## [T1】简介](#intro)

大多数时候，工程师得到的是他们无法采取行动的糟糕的错误报告。这是因为“普通人”通常不熟悉哪些信息可以帮助工程师更快地解决问题。

我们试图编写一份任何人都容易理解的指南。请随意发送以下信息，尤其是当您收到一份几乎没有任何有用信息的错误报告时。

## 缺陷报告模板

*   标题
*   复制步骤*
*   结果和预期行为
*   屏幕录制或截图
*   系统信息*
    *   操作系统
    *   浏览器
    *   设备
    *   时间(包括时区)
*   用户 ID[如果相关]
*   影响[如果已知]

**一些屏幕录制工具会自动包含这些信息(详见下文)。*

## 如何使用这个模板

当报告一个 bug 时，需要理解的最重要的事情是，读者应该能够仅用所提供的信息在他们的计算机上再现它。并不总是可能重现 bug，但是最好提供尽可能多的信息。

**Title**

简要描述问题，例如“计算器:乘法结果不正确”。尽量简洁，但这里要具体。例如，“损坏的登录页面”不能帮助读者了解问题是否已经被报告，以及到底是什么问题:它是不可访问的吗？布局是不是破了？难道只是其中一个头条的错别字？

**再现步骤**

描述一下在遇到 bug 之前你采取了什么行动。它可能看起来像这样:

1.  去 ExampleURL.com
2.  点击按钮 A
3.  选择选项
4.  类型“123”
5.  点击输入

最好尝试自己重现 bug，这样可以确定提供的步骤会导致您报告的 bug。

**结果和预期行为**

将你的行动结果与你预期的结果进行比较。示例:

*   X 的事情应该发生。事实上，Y 发生了
*   我得到一个错误“404。找不到页面。

**屏幕录制或截图**

有许多屏幕录制工具供您选择。Mac 用户可以使用 QuickTime(见教程[此处](https://support.apple.com/en-gb/guide/quicktime-player/qtp97b08e666/mac))或者按下 Shift-Command-5。在 Windows 10 上你可以使用 Xbox 应用程序(参见教程[这里](https://www.youtube.com/watch?v=LKcDwotueAM))。

为错误报告制作屏幕记录的更快方法是使用专用工具(即 [Bird](http://birdeatsbug.com) 或 [BugReplay](http://bugreplay.com) )。与视频一起，它会自动捕捉技术信息，帮助工程师更快地解决问题。这样你也不需要担心提供系统信息和输入重现 bug 的步骤——它们也会被自动记录。

在某些情况下(如登录页面上的打字错误)，没有必要录制视频-截图就足够了。要制作一个，你可以使用你的操作系统内置的工具。看如何在 Mac 上制作截图[这里](https://www.youtube.com/watch?v=NbB3Cm2ejkg)，Windows - [这里](https://www.digitaltrends.com/computing/how-to-take-a-screenshot-on-pc/)。

**系统信息**

操作系统(包括版本):点击[此处](https://whatsmyos.com/)了解此信息。

浏览器(含版本):点击[此处](https://www.whatsmybrowser.org/)了解此信息。

设备:桌面/平板/移动

时间(包括时区):至少是你遇到 bug 的大概时间。

**用户 ID【如果相关】**

如果您或错误报告者在错误发生时已经登录，请提供帐户的唯一标识符。通常是用户名或电子邮件。

**影响【如果已知】**

如果你知道有多少人受到这个 bug 的影响，或者因为这个 bug 损失了多少收入，一定要提供这些信息。它将帮助工程师和产品经理分配正确的优先级。

## 最后的想法

如果您希望您或您的客户的问题得到快速解决，请尝试提交一份好的错误报告——这份报告可以根据提供的信息轻松重现错误。工程师掌握的信息越多，他们实施修复的速度就越快。

对于产品团队来说，收到下面这样的邮件并不少见:

*   什么都不管用
*   页面未加载
*   一切都崩溃了
*   我什么也没看见
*   x 坏了
*   等等。

请不惜一切代价避免这些。这将有助于你和人们修复错误，以节省时间和挫折。