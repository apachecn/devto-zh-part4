# 我是如何通过打开我的第一个拉请求开始为开源做贡献的

> 原文：<https://dev.to/nirebu/how-i-started-contributing-to-opensource-by-opening-my-first-pull-request-1g28>

这比预期的要容易。就像，真的比预期的要简单。

## 问题

我想建立一个*零配置或几乎零配置的*文档系统，在我们的办公室使用，远离 ODT 的地狱，分散在文件服务器上的 PDF 文件。你知道我在说什么😁

长话短说，当我发现这个基于目录结构自动生成侧边栏和导航条的[插件](https://github.com/ozum/vuepress-bar)时，我决定使用 [Vuepress](https://v1.vuepress.vuejs.org/) :我认为**约定优于配置**的方法是最强大和最简单的方法之一，尤其是如果你希望团队能够从一开始就编写文档的话。

在测试阶段，我注意到在不同部分导航时的奇怪行为(在 Vuepress 行话中:顶栏)，由带有`nav.directory`名称的目录定义:那里的每个链接都会导致 404。

你有没有经历过这样的时刻，你对自己说

> 有没有可能，即使按照文档/指南/教程的最后一段，我也无法复制作者展示的令人惊叹的结果？

去过吗？我去过那里很多次，但这次我想我可以深入了解它，也许可以帮助解决一个问题，这样其他人就不必经历这个障碍。

## 调试 Vuepress

我发现的第一个让我走上正轨的证据是看到导航栏中链接的链接目的地格式不正确或者完全错误，没有预先考虑基本 URL 并且缺少页面名称:

```
# rendered href
https://nav.directory/.html

# desired href
http://localhost:3000/nav.directory/index.html 
```

我跳上了令人敬畏的 [Vue 开发工具](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)，开始挖掘开发服务器创建的 Vue 组件道具，注意到由`vuepress-bar`插件生成的`$site.themeConfig.navbar`和`$site.themeConfig.sidebar`参数有畸形的值，比如:

```
"sidebar" : {
  "/": {...},
  "\nav.directory\": {...},
  "\nav.directory2\": {...}
} 
```

字符`backslash`把我带到了正确的轨道上，因为这是 Windows 默认使用的目录分隔符。为了消除任何疑虑，我在 Linux Docker 容器中尝试了完全相同的配置，实际上它像预期的那样工作。

## 开盘发行

确定了可能的原因，我去了`vuepress-bar` Github repo，打开了这个问题。

这个过程非常简单:

*   已选择问题选项卡
*   确保没有其他人提出同样的问题(我猜现在没有多少 Windows 用户)
*   点击了`New issue`按钮
*   为了不浪费开发人员的时间，我尽量做到描述性强但同时又简洁，包括呈现的 html 和期望结果的例子

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)Windows 上错误的目录分隔符 #2](https://github.com/ozum/vuepress-bar/issues/2) 

[![nirebu avatar](img/fc8fa3e73441977e9aa78ea44ebf9c6a.png)](https://github.com/nirebu) **[nirebu](https://github.com/nirebu)** posted on [<time datetime="2019-07-23T10:26:17Z">Jul 23, 2019</time>](https://github.com/ozum/vuepress-bar/issues/2)

我试图使用你的插件来自动构建导航条和侧边栏，但是我在使用结构`nav.foldername`构建导航条时发现了一个问题。

插件会将`$themeConfig.nav[].link`构建为`\nav.foldername\`，从而破坏导航和侧边栏加载。

我确定了在 Windows 上的原因，因为相同的配置，在 Linux 虚拟机上运行没有问题，在`nav`对象和`sidebar`组键中生成参数`/nav.foldername/`。

有没有办法把正确的浏览器分隔符硬编码为`/`？

非常感谢

[View on GitHub](https://github.com/ozum/vuepress-bar/issues/2)

## 寻找可能的解决办法

在`node_modules/vuepress-bar`的实际插件文件夹中挖掘，在看了几分钟比我更有经验的其他人的代码后，我终于找到了*冒犯*的那一行。

三次尝试后，用一些不同的配置进行测试，我最终决定了一个解决方案，这可能是快速和肮脏的，但至少似乎解决了问题。

## 通过提出拉取请求来建议我的修复

为了向作者提出我的修改，我需要首先通过点击 repo 顶部的`fork`按钮来分叉存储库。接下来，我通过命令
在本地克隆了它

```
git clone https://github.com/nirebu/vuepress-bar 
```

在 VS 代码中打开文件夹，进行与我在`node_modules`中所做的相同的更改，并使用这些命令
将更改推送到我的分叉回购中

```
git add . # to add all the files changed, in this case just one
git commit -m "Adjusted the nav link separator on Windows" # to describe what my change would do if applied
git push origin master # to actually upload my changes to my forked repository 
```

现在只需要打开实际的拉请求，通知作者我的更改。为此，我单击了存储库导航栏中的`Pull requests`按钮，然后单击了`New Pull Request`。

要选择我的存储库作为变更的来源，我需要选择`Compare across forks`链接，然后选择我的分叉存储库`master`分支的`HEAD`提交(最新的)一样简单。结果是这样的:

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 调整了导航链接分隔符，在 Windows 上也能工作 #3](https://github.com/ozum/vuepress-bar/pull/3) 

[![nirebu avatar](img/fc8fa3e73441977e9aa78ea44ebf9c6a.png)](https://github.com/nirebu) **[nirebu](https://github.com/nirebu)** posted on [<time datetime="2019-07-23T23:49:04Z">Jul 23, 2019</time>](https://github.com/ozum/vuepress-bar/pull/3)

我提出了我打开的第二个问题的解决方案。我用一个普通的替换来修复窗口分隔符`\`和`nav`链接中的`/`字符。

[View on GitHub](https://github.com/ozum/vuepress-bar/pull/3)

唷，好了。

现在就等作者马上审核通过，评论或者拒绝我的拉取请求吧。

## 关闭思绪

为别人的项目做贡献可能是一个令人生畏的过程，因为除了技术方面，还有很多心理因素，比如冒名顶替综合症和害怕被拒绝。

我肯定我的提议不是最好的，也不是最优雅的，但至少我已经克服了最初的障碍，并进行了尝试。当我变得更加精通我的编程技能时，我会确保为其他人的项目做出更多的贡献:这是一种回报已经给了我很多的社区的方式。