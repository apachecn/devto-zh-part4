# 我的第一个 Rails 应用。

> 原文：<https://dev.to/michaellalatkovic/social-grads-my-first-rails-app-10p0>

末日到了！良好的...至少结束了 Rails 部分的[Learn.co](https://flatironschool.com)软件工程课程。

为了这个项目，我创建了一个名为 **Social Grads** 的网络应用——一个提交研究生申请的社交工具。这个应用程序的灵感来自于[学生医生网](https://forums.studentdoctor.net)的论坛部分——如果你从未去过这个网站，它太神奇了。

当我自己申请研究生院的时候，我花了很多时间在这个网站上阅读我能看到的任何东西。我最喜欢的(也是最受欢迎的)两个论坛是“无所不包”、“我有什么机会”和“线程！”和“申请，面试，拒绝，参加”——后者是真正启发我创造社会梯度。我知道向下滚动阅读每一个帖子的痛苦，希望找到关于谁申请了什么学校，他们的背景是什么，最重要的是，他们被录取了吗？

[![](img/c22f8ecc305e11588b469ff90d75c2d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jIS6qvZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6fbo7pkmf7i1id69rasi.png)

Social Grads 利用这个论坛创建了一个工具，用户可以创建一个帐户，并将其信息添加到用户资料中。用户可以注册一个新账户或通过 GutHub 登录。

[![](img/16a4fbdad505bec52ff6527560616b5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3GDTXvkZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vo5b5t7ivgdwz08wu14o.png)

[![](img/858888156a1eeeec780ac492b2f1261b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wO88no6n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iq3v21n5fgo3t0mly6rf.png)

登录后，他们可以为他们申请的每个项目添加提交内容，如下所示:

[![](img/55965a124f09a21971cc8033103a75a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fALeIijd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l78xywzb7b4e1w0vfk5z.png)

这不仅是一种跟踪你申请的所有项目的简单方法，你还可以轻松地搜索网站上的其他用户——查看他们的个人资料，查看他们创建的提交内容，甚至查看大学的个人资料，其中包含该校积极提交内容的社会毕业生社区提交的所有内容的摘要。

### 倒影

**用户体验&网址**

在我对应用程序和视图页面的其余部分做了大量工作之后，我添加了嵌套路线。当我检查我的应用程序时，我注意到当我的应用程序重定向到正确的视图时，我的 URL 并不总是遵循一致的路径，这对用户导航应用程序是有意义的。从本质上讲，该应用程序运行正常，但一旦部署到现实世界中，可能会造成混乱。这个练习强调了不要迷失在从开发人员的角度考虑我们的应用程序中是至关重要的——但是要永远记住从用户的角度考虑设计。

**关注 MVP**

我看到自己陷入的一个挑战是被我能添加或想添加的所有功能淹没了。我会花时间仔细考虑每一件事，然后意识到几个小时过去了，我还没有添加一个功能。我了解到，即使有更多的潜力，也最好是达到 MVP 来满足项目的要求。为了在这个旅程中保持动力，我需要专注于自己，并且知道我可以在以后继续这个项目。这是我在为这个项目构建最终项目时必须记住的事情。

**提交 F.R.E.Q.U.E.N.T.L.Y**

这个项目教给我的最大的一个教训是，在提交时要更加深思熟虑，更加频繁。我习惯于将特性打包提交(登录、注销、新建、编辑等)...)

这个方法在一段时间内奏效了——干净利落。如果我想看到添加一个特性的所有代码，我可以在一次提交中完成。然而，这并不是提交的最佳用例。

这个项目进行了三分之二，我的主分支和我的本地目录不知何故没有对齐。我仍然有代码没有被推上来，因为我在等待完成一个特性。经过多次调试(git stash，git pull，git rebase....)在我导师的帮助下，唯一管用的就是:

git 添加。
git commit -m“请工作。”
推力

我在一次提交中强制执行了我的所有代码——幸运的是，这修复了我的合并冲突，但是我丢失了一些代码。

吸取教训。经常提交，即使只是添加一个链接到一个视图页面。以后你会感谢自己的！

快乐编码，
麦克拉