# 如何在你的静态网站上获得一个评论区

> 原文：<https://dev.to/brianemilius/how-to-get-a-comment-section-on-your-static-site-5j8>

*本文原载于[www.brianemilius.com/static-site-comments/](http://www.brianemilius.com/static-site-comments/)，2019-07-23。访问[www.brianemilius.com/blog](http://www.brianemilius.com/blog)了解更多内容。*

* * *

在这篇文章中，我将向你展示如何在一个没有后台的静态网站上实现一个评论区。但是首先，让我解释一下什么是静态站点，什么是后端。

## 静态站点

静态网站是没有动态内容的网站。通常，它们完全是用 HTML、CSS 和 JavaScript 编写的。像这样的网站不从外部来源获取内容。每一页都是独一无二的，将独立于网站上的其他页面。

静态站点的优势包括

*   易于开发
*   易于托管
*   在慢速连接上快速工作

缺点可能是

*   低可扩展性
*   难以实施设计变更

web 开发的最新趋势是使用静态站点生成器。

静态站点生成器允许开发人员使用模板和 web 组件，最终生成静态 HTML 页面。

一些流行的静态站点生成器有

*   [己糖](https://hexo.io/)
*   哲基尔
*   盖茨比

## 后端

web 开发中的后端是用于生成数据以及接收和存储数据的。这包括数据操作。

这方面的一个例子是一个提供天气预报的网站。

后端从气象卫星、气象气球、气象站等接收大量数据。这些数据需要翻译、解释并存储在数据库中。

当访问者通过网站请求预测时，后端将选择适当的数据，将其格式化以便呈现，并将其发送给访问者。

拥有后端的优势可能是

*   动态内容创建导致更多的访问
*   支持用户之间的交互
*   内容和设计的分离

拥有后端的一些缺点可能包括

*   根据项目的范围，托管可能会变得非常昂贵
*   后端面临黑客恶意攻击的高风险，因此您需要了解服务器安全性

## 没有后端的评论

现在我们已经知道了术语，让我们来看看以下问题的解决方案:

> 我想在我的静态网站上有一个评论部分，但我没有后端，所以我不知道在哪里存储评论。

有很多方法可以解决这个问题。他们大多使用社交媒体平台作为引擎盖下的引擎。隐私是我一直关心的问题，所以我想在不使用跟踪我的用户的技术的情况下解决我的问题。

GitHub 和 [Gitalk](https://github.com/gitalk/gitalk/) 前来救援。

GitHub 是代码协作的首选平台。GitHub 上的每个项目都存在于一个叫做资源库的地方。存储库可以有相关的问题，您可以对问题进行评论。这是我们将要利用的技术。

为此，我们将使用一个叫做 Gitalk 的漂亮的小开源包。

只要你有一个 GitHub 帐户，Gitalk 就可以让你在任何静态页面上添加一个全功能的评论区。然而，这确实限制了对编程感兴趣并且已经拥有 GitHub 帐户的人使用你的网站评论部分。这就是为什么我建议你使用这种方法，如果你的网站主题是关于编程，网页开发或类似的。

### 第一步

首先，你需要去 GitHub 创建一个 OAuth 应用。

1.  进入 GitHub 设置页面，点击左侧菜单中的“开发者设置”,然后点击菜单中的“OAuth 应用”。
2.  点击顶部的“新建 OAuth 应用程序”按钮。
3.  用您的应用程序名、您的网站的 URL 和一个授权回拨 URL 填写表单。在这种情况下，回调 URL 与您的网站 URL 相同。
4.  注册 OAuth 应用程序后，您将收到一个客户端 ID 和一个客户端密码。保存这些。

### 第二步

创建公共存储库。这是您的评论将被保存的地方。

1.  在 GitHub 上，点击右上角的加号并选择“新建库”
2.  填写表格。确保选择公共，而不是私有。您唯一需要填写的是存储库名称。它可能是类似“我的网站-评论”的东西。
3.  单击绿色的“创建存储库”按钮。

这就是你在 GitHub 上需要做的所有设置。

### 第三步

现在你需要复制并粘贴一些代码到你的网页上。

在需要评论部分的页面上，将其放在标题部分。

```
<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

这将把样式表和 Gitalk 脚本加载到页面中。

接下来，您需要决定在页面的哪个位置放置评论。把这段代码放在你需要的地方。

```
<div id="gitalk-container"></div> 
```

Enter fullscreen mode Exit fullscreen mode

最后，将这个脚本插入页面底部，就在`</body>`标签之前。

```
<script>
const gitalk = new Gitalk({
  clientID: 'GitHub Application Client ID',
  clientSecret: 'GitHub Application Client Secret',
  repo: 'GitHub repo',
  owner: 'GitHub repo owner',
  admin: ['GitHub repo owner and collaborators, only these people can initialize github issues'],
  id: location.pathname,
  distractionFreeMode: false  // Facebook-like distraction free mode
})

gitalk.render('gitalk-container')
</script> 
```

Enter fullscreen mode Exit fullscreen mode

将步骤 1 中的客户端 ID 和客户端密码插入到脚本中，将`owner`和`admin`更改为您的 GitHub 用户名，将`repo`更改为您在步骤 2 中选择的存储库的名称。

### 第四步

在浏览器中打开每个带有注释部分的页面，然后单击 Init 按钮。这将为 GitHub 上的评论区制造一个问题，让你的访问者使用新的功能。

我在我的网站上使用这种方法，然而，我用盖茨比创建了我的网站。这意味着我必须使用稍微不同的方法。如果你有兴趣，可以看看 GitHub 上的[我的网站库，看看我是怎么做到的。](https://github.com/BrianEmilius/my-personal-website)

请在下面的评论中告诉我你对这个方法的想法！

[rawpixel.com 创作的封面照片-](https://www.freepik.com/free-photos-vectors/people)【www.freepik.com T2】