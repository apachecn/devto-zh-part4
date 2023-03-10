# 使用 Git(和 GitHub)的这些移动应用程序随时随地使用 Git

> 原文：<https://dev.to/jcabot/git-on-the-go-with-these-mobile-apps-for-git-and-github-3e5o>

我想看看可用于管理我的 [Git](https://git-scm.com/) 库和 [GitHub 项目](https://github.com/SOM-Research)的移动应用。我本以为只会找到几个选项，但令我惊讶的是，当你需要在旅途中访问 git 或 GitHub 时，有几十个应用程序声称是你的理想解决方案。

只要在谷歌 Play 商店以“git”为关键词进行搜索，你会立即得到 30 多个结果。这包括几个只是在通勤时学习 git 的应用程序(查看这个[开源快速 git 参考指南](https://github.com/easyintent/quickref))。以及其他已经被放弃或仍处于 alpha 阶段的产品。

经过我的(主观)清理，我为 git 和 GitHub 推荐的应用程序的候选名单如下。

## 手机 Git 应用

Pocket Git 是一个强大的 Android 独立 Git 客户端，具有所有显而易见的特性(克隆库、签出分支、区分视图、创建文件、提交、标记、....)以及对 HTTP 和 SSH 协议、密码和私钥(带密码短语)的支持。当然，它不是免费的，但它的价格不到 3 美元，作为一个开发者，我相信你会理解你的开发者伙伴需要吃饭，对吗？。在这篇文章的特色图片中可以看到 PocketGit 的实际应用。

在苹果方面， [WorkingCopy](https://itunes.apple.com/us/app/working-copy/id896694807?mt=8) 是 iPad 和 iPhone 上最受欢迎和赞赏的 git 客户端。正如他们在应用程序描述中令人信服地解释的那样，有时你只想更新一个 TODO 文件或对你的 Jekyll 站点进行调整。有时候你只需要添加一个设计师下班后发来的文件。WorkingCopy 是实现这一点的理想选择，这样你就不会忘记做这些更改(或者不得不等到大屏幕可用时)。另一个有趣的特性是提交的图表。这个图可以让您缩小提交树的概览，或者放大每个提交的细节，其速度和美观是桌面 Git 应用程序所没有的。这个应用程序可以免费浏览，但你需要企业版才能推送提交。

[![WorkingCopy git app for iPhone](img/46c202298838260f0902e524516be75d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W6-SuaqW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://livablesoftware.com/wp-content/uploads/2018/06/workingcopy.png)

FastHub 是另一个受欢迎的选择，但目前似乎正在经历重大的重构，所以等待新的主要版本发布是明智的。 [FastHub-libre](https://f-droid.org/en/packages/com.fastaccess.github.libre/) 是 FastHub 的牙线叉。

更极端的是，除了 Git 客户端，您甚至可以尝试使用用户和用户组密码以及存储库的 ssh 认证在您的移动设备上运行 Git 服务器。

## 手机 GitHub 应用程序

对于 Android 设备，我最推荐的是 ForkHub。 [ForkHub](https://github.com/jonan/ForkHub) 是基于废弃官方 app 的 Android 开源 GitHub 客户端。它使用建立在 [API v3](https://developer.github.com/v3/) 之上的 [GitHub Java API](https://github.com/eclipse/egit-github/tree/master/org.eclipse.egit.github.core) 在应用中提供所有典型的基于网络的 GitHub 功能。你甚至可以将代码片段作为[GitHub gist](https://gist.github.com/)共享。下载量超过 50K，是目前最受欢迎的 Android GitHub 应用。如果你不喜欢 ForkHub，你也可以试试 [OpenHub、](https://github.com/ThirtyDegreesRay/OpenHub/) GitPoint(见下文)或 [OctoDroid](https://play.google.com/store/apps/details?id=com.gh4a) ，尽管后者更侧重于将 repos/projects 可视化，并且只提供限量版功能。

作为其中任何一个的补充，你也可以安装 [Git Social](https://play.google.com/store/apps/details?id=com.naturalwarren.githubsocial) (帮助你跟上你关注的 GitHub 用户最近的活动)。

[![ForkHub GitHub App for Android](img/cbc35dc81f9eee6317797c70cfe8e1df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p925fKPW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://livablesoftware.com/wp-content/uploads/2018/06/forkhub-1-1024x768.png)

对于苹果设备，我的第一选择是 [GitPoint](https://gitpoint.co/) (也适用于 Android)。GitPoint 由 React native 构建，是一个免费、开源的“口袋里的 GitHub”选项(正如他们自己定义的那样)，具有出色的 UI。您可以查看存储库和用户信息，控制您的通知，管理您的问题和请求。 [GitHawk](https://github.com/GitHawkApp/GitHawk) 是另一个很好的选择，尤其是帮助你清理你的 GitHub 通知。它提供了丰富的评论支持(包括表情符号反应)来尽可能快地回应他们。[GitHub 的葡萄](https://itunes.apple.com/app/apple-store/id1371929193?mt=8&ref=producthunt)是 GitHub 客户端类别中最新的苹果应用商店之一。你可以做所有典型的 GitHub 动作(寻找回购，明星，观看，关注开发者，..)包括写/管理评论，甚至在拉请求中。你不能做的是直接修改代码(但对于添加你已经有 Git 类别的上述应用程序)。

如果你只需要一个应用程序来管理你的 GitHub 问题，GitShot 会帮你搞定。如果你只想在手机上得到一个提醒，每当新的 GitHub 通知到达时就给你发信号，那么[布里吉特](https://itunes.apple.com/us/app/brigitte-alert-for-github/id1439589577?mt=8&ref=producthunt)就足够了。

[![GitPoint - GitHub app for the iPhone](img/cbee93724c13f30188aea18a54f955b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y8cyNM4O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://livablesoftware.com/wp-content/uploads/2018/06/gitpoint.png)

## 有没有 Bitbucket 或者 GitLab 的 app？

每个需求都有一个应用程序。 [GitLabControl](https://gitlabcontrol.com/) 帮助你在任何 iPad/iPhone 设备上管理你的 [GitLab](https://gitlab.com/) 项目。相反，如果你使用的是 [BitBucket](https://bitbucket.org/) ，有几个选项可供选择。最流行的一个叫 [Bitbeaker](https://play.google.com/store/apps/details?id=fi.iki.kuitsi.bitbeaker) ，是 Android 的开源 Bitbucket 客户端。Bitbeaker 既不是完整的 Git，也不是 Mercurial 客户端。而是使用 Bitbucket 的 REST API。Bitbasket 是 Android 的潜在替代品，CodeBucket 是 iPhone 的潜在替代品。对于在 GitHub 和 BitBucket 中都有项目的人来说， [OmniCode](https://play.google.com/store/apps/details?id=com.keepworks.omnicode) 为这两个平台提供了统一的接口。

[![Bitbeaker - a bitbucket client app](img/f47ea5874502123a5281d0b059289f3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zIxxHknT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://livablesoftware.com/wp-content/uploads/2018/06/bitbeaker.png)