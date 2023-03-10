# 我新网站的幕后

> 原文：<https://dev.to/geekygirlsarah/behind-the-scenes-of-my-new-website-3044>

<figure>[![Screenshot of my new website along with the web developer tools open](img/3c64007a62d2a41c17c2d699f641c9c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RK-QpJDh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geekygirlsarah.com/asseimg/2019/09/screenshot-new-website.jpg) 

<figcaption>我的新网站截图连同网页开发工具一起打开</figcaption>

</figure>

我做到了！大约花了两年时间，我终于坐下来做了，但是我把三个域上的两个 Wordpress 站点转换成了一个静态站点！

我用它做了一些很酷的事情，所以我想谈谈我做了什么。

(注:我在这里说了很多技术。这是我自己的观点，没有人付钱让我说这些。)

## 之前

<figure>[![Screenshots of my old sites side-by-side](img/83f9ead7ab9bee157637c6aa701ee452.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qOqWR1A2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geekygirlsarah.com/asseimg/2019/09/old-sites-side-by-side.jpg) 

<figcaption>我的旧网站截图并排</figcaption>

</figure>

我最初拥有 sarahwithee.com 域名，并主要用它来托管我在大学中期建立的一个网站。这正是我所需要的。我不太擅长设计，几年前我的朋友安德鲁为我做了这个草书，所以我决定找一个主题并融入其中。我的朋友 [Josepha Haden](https://twitter.com/JosephaHaden) 在她的网站(Wordpress)上有一个我非常喜欢的[主题，在修改了一些颜色和背景图片后，我把它用在了我的网站上。当时我没有太多事情要做，所以](https://wordpress.org/themes/sela/)[成为了一个不错的个人网站](https://web.archive.org/web/20151007072051/http://sarahwithee.com/)。

后来，我意识到 geekygirlsarah.com 是我的“品牌”，我确实应该拥有它，于是收购了它。在我和几个朋友的博客“二元女孩”真的没有获得关注后，我决定(再次)开一个博客。(主要是我在发表东西。)我导入了我的旧内容，并开始慢慢地在上面写新内容。

## 更新的原因

有一次，我帮助管理的一个在 Wordpress 上运行的网站被黑了，整个网站都被毁了。花了很长时间才恢复过来，所以我最终在我的两个 WP 网站上安装了一些安全软件。它工作得很好，但我会看统计数据，看看他们被黑客攻击的频率，这是相当令人不安的。我不知道这是因为我现在有更多的观众，还是因为我的网站对 WP 有吸引力(或者两者都有)，但慢慢地我开始怀疑我是否需要其他东西。

我知道很多人都在炒作静态网站。我完全赞同这个想法，但有一件事让我退缩了，那就是分析。虽然我不是一个巨大的统计观察家(和喜欢/关注/等。不是我的东西)，我想要的东西，但不想要谷歌分析。我看到有一些服务，我可以支付，但当时没有能力支付他们的费用。这让我有些犹豫。

大多数情况下，这个网站只是觉得过时了。它太旧了，人们来我的网站找的东西并没有被突出显示或者很容易找到。管理内容变得越来越困难(WP 中的表格很麻烦，有几个页面有重复的内容)。我想要更容易更新的东西。

## 升级

我知道我的主要选择似乎是[杰基尔](https://jekyllrb.com/)(基于 Ruby)[雨果](https://gohugo.io/)(基于 Go)[盖茨比](https://www.gatsbyjs.org/)(基于 React/Node)，所有这些我都不熟悉..我试了一下这三种方法。我真的很喜欢雨果。我开始长途跋涉寻找一些雨果主题，找到了一些我喜欢的，但在这么多的尝试后，我不能让他们完全按照我的想法去做。我在文档上钻研得太深了，甚至无法完成一些简单的事情，我厌倦了，放弃了。

有盖茨比，但是看了很多文档之后，感觉对我来说准入门槛太大了。所以我和哲基尔一起玩。找到了一些主题并让它们发挥作用。在尝试了十几次之后，我最终选定了[最小错误](https://jekyllthemes.io/theme/minimal-mistakes)，这是一个非常灵活的主题，我可以用它来做我想做的大部分事情。

我知道我需要把所有东西从 Wordpress 转换到 Markdown，这听起来很痛苦。我找到了一个 [WP 插件](https://wordpress.org/plugins/jekyll-exporter/)来帮助解决这个问题，但是很多都没有很好地转换。我不得不翻遍每一页(超过 24 页，后来我精简到 15 页)和所有的帖子(超过 55 篇)。但是我让所有的东西都工作起来，我的网站也开始运转起来。

我知道内容重写是需要发生的事情。我花了一些时间来检查每一页，保留我喜欢的部分，删除我不喜欢的部分，并把它变成第一人称而不是第三人称。(我认为这很有帮助，因为这是我的网站，但它也可能是一个类似投资组合的东西，所以我希望它听起来更像是我做的，而不是别人为我做的)。我想如果我把简历和博客放在这里，第三人称似乎也不合适。我更新了所有的 bios 和页面，一切就绪。

最后，我希望它是开源的，所以我把它扔在了 [GitHub](https://github.com/geekygirlsarah/geekygirlsarah.com) 上。我也从一些朋友那里知道了[网络生活](https://www.netlify.com/)，所以我尝试了一下，发现部署站点(并自动更新)非常容易，所以切换很容易。(另外:我本来打算用[的 Matomo](https://matomo.org/) 进行分析，但是不到一个月 [Netlify 就以四分之一的价格公布了他们自己的统计数据](https://www.netlify.com/docs/analytics/)。我马上就跳了下去！)

## 一些很酷的部分

我知道很多人通过我的会议演讲认识我，所以拥有最新的笔记、幻灯片、视频等是很重要的。当人们去那里看东西的时候。我浏览并更新了所有页面，包括最新信息、版本历史、代码更新、GitHub 内容的链接等等。

还有一个[会谈时间表](https://geekygirlsarah.com/speaking/)，这是我直接用 HTML 管理的。这很烦人。我知道杰基尔可以根据数据文件进行一些[操作。我开始将对话加载到一个 YAML 数据文件中(完整文件在这里是](https://jekyllrb.com/docs/datafiles/)):

```
- date: 2019-05-11
  end_date: 2019-05-12
  event: "!!Con"
  location: New York City, NY
  type: Lightning Talk
  title: I Built an Artificial Pancreas!
  event_url: http://bangbangcon.com/
  talk_url: https://sarahwithee.com/speaking/building-an-open-source-artificial-pancreas/
- date: 2019-06-07
  end_date: 2019-06-08
  event: Self.Conference
  location: Detroit, MI
  type: Session
  title: Building an Open Source Artificial Pancreas
  event_url: http://selfconference.org/events/2/sessions#speaker_2
  talk_url: /speaking/building-an-open-source-artificial-pancreas/ 
```

Enter fullscreen mode Exit fullscreen mode

我本打算为过去和未来的谈话制作文件，但后来发现基于日期，我可以过滤东西。我写了这样一个简介，我为这两个表格中的漂亮打印的演讲感到自豪，一个是即将到来的，另一个是过去的演讲(见完整文件[这里](https://github.com/geekygirlsarah/geekygirlsarah.com/blob/master/_pages/speaking.md) ):

```
{ % assign today = "now" | date: "%Y-%m-%d" % }

| Date | Event | Location | Talk Type | Talk Title | 
|------|-------|----------|-----------|------------|
{ % for item in site.data.conftalks % }
  { % assign event_date = item.date | date: "%Y-%m-%d" % }
  { % if event_date >= today % }
    { % if item.date == item.end_date % }
      { { item.date | date_to_xmlschema | date_to_string: "ordinal", "US" } }
    { % else % }
      { { item.date | date_to_xmlschema | date_to_string: "ordinal", "US" } } - { { item.end_date | date_to_xmlschema | date_to_string: "ordinal", "US" } }
    { % endif % }
    | { % if item.event_url % }
        <a href="{ { item.event_url} }" target="_blank">{ { item.event } }</a>
      { % else % }
        { { item.event } }
      { % endif % }
    | { { item.location } } 
    | { { item.type } } 
    | { % if item.talk_url % }
        <a href="{ { item.talk_url} }">{ { item.title } }</a>
      { % else % }
        { { item.title } }
      { % endif % }
{ % endif % }{ % endfor % } 
```

Enter fullscreen mode Exit fullscreen mode

(注意:为了给博客渲染，也为了增加可读性，我增加了各种间距。检查[完整文件](https://github.com/geekygirlsarah/geekygirlsarah.com/blob/master/_pages/speaking.md)以获得正确格式化的版本。)

如果日期是今天或以后，这将打印表中的项目。我基本上做了另一个类似的块，如果日期是在过去。一旦我把所有的东西都放在一行上(因为 Markdown 中的表格都是这样格式化的)，想到我可以把一个会议添加到一个数据文件中，网站就会自动生成，真是太酷了。(这个我不要太觉得好笑，我是程序员。但是简化这个感觉太棒了！)

<figure>[![Screenshot of the Latest Podcasts section of the home page](img/a64f72520721d29c704b798b6f262382.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WoP9XZte--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geekygirlsarah.com/asseimg/2019/09/screenshot-feature-block.jpg) 

<figcaption>首页最新播客栏目截图</figcaption>

</figure>

对于主页，主题包括一个`feature_row`，它允许在首页上有一个左对齐或右对齐的行。我对它做了一点修改，添加了一个`feature_block`，它可以让我显示一些数据条目。我选择把即将到来的演讲、最新的播客和博客文章放在那里。我可以从相同的 YAML 列表中提取数据，只需提取我想要的最后 3 项(或任意多项)并将其显示为一个列表。

看起来是这样的:

```
{ % assign type = include.data_type % }
{ % assign show_number = include.show_number % }

{ % if type % }
  { % assign today = "now" | date: "%Y-%m-%d" % }{ % assign count = 0 % }{ % if f.url contains "://" % }{ % capture f_url % }{ { f.url } }{ % endcapture % }{ % else % }{ % capture f_url % }{ { f.url | relative_url } }{ % endcapture % }{ % endif % }
  <ul>
    { % if type == "posts" % }
      { % for item in site.posts % }
        { % if count < show_number % }
          { % assign date = item.date | date: "%Y-%m-%d" % }
          <li>{ { date | date_to_string: "ordinal", "US" } }: <a href="{ { item.url } }" target="_blank">{ { item.title } }</a></li>
          { % assign count = count | plus:1 % }
        { % endif % }
      { % endfor % }
    { % elsif type == "talks" % }
      { % for item in site.data.conftalks % }
        { % if count < show_number % }
          { % assign date = item.date | date: "%Y-%m-%d" % }
          { % if date >= today % }
            <li>{ { date | date_to_xmlschema | date_to_string: "ordinal", "US" } }: <a href="{ { item.event_url } }" target="_blank">{ { item.title } }</a> ({ { item.type } }, { { item.event } })</li>
            { % assign count = count | plus:1 % }
          { % endif % }
        { % endif % }
      { % endfor % }
    { % elsif type == "podcasts" % }
      { % for item in site.data.podcasts reversed % }
        { % if count < show_number % }
          { % assign date = item.date | date: "%Y-%m-%d" % }
          <li>{ { date | date_to_xmlschema | date_to_string: "ordinal", "US" } }: <a href="{ { item.url } }" target="_blank">{ { item.episode } }</a> ({ { item.show } })</li>
          { % assign count = count | plus:1 % }
        { % endif % }
      { % endfor % }
    { % endif % }
  </ul>
{ % endif % } 
```

Enter fullscreen mode Exit fullscreen mode

(注意:同样，为了可读性和渲染，对其进行了修改。完整文件见[此处](https://github.com/geekygirlsarah/geekygirlsarah.com/blob/master/_includes/feature_block)。)

对此的简单解释是:

1.  如果有类型(帖子、演讲、播客)传入，显示该块(否则隐藏它)
2.  循环浏览向前排序(演讲)或向后排序(播客/帖子)的项目
3.  如果项目与未来/过去的日期匹配，则显示它
4.  增加一个计数器
5.  达到 3 个(或更多)项目后，退出循环

然后我把这三个放在一起，就可以在主页上显示所有三个区块了！

```
<div class="home_feature_table_cell">
  <h2>Upcoming Talks</h2>
  { % include feature_block data_type="talks" show_number=3 % }
  <a href="/speaking/" class="btn btn--primary">All Talk Schedule</a>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 正在部署

<figure>[![Screenshot of the Deploys page of Netlify](img/bea4bfea79466bae150a245ad7297281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DWU7_G1I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geekygirlsarah.com/asseimg/2019/09/screenshot-netlify-builds.jpg)

<figcaption>Netlify</figcaption>

</figure>

部署页面截图

我知道如果我摆脱了 Wordpress，我可以继续托管我自己的服务器，或者我可以使用 Netlify 之类的东西来为我做这一切。Netlify 是一个静态网站托管服务。当你推送内容时，他们不仅发布内容，还使用各种内容交付网络(cdn)使其速度超快。个人计划是

免费的(你需要为额外的服务或更大的网站付费)。挺棒的！

因为它[连接到 Git 和 GitHub](https://www.netlify.com/docs/continuous-deployment/) ，我可以直接推送到我的主分支，它会自动重建 Jekyll 站点并推出新版本。它被配置为运行正确的命令(大部分只是`jekyll build`),因为它缓存了所有的依赖项，所以速度非常快。我[只需要在我的根](https://github.com/geekygirlsarah/geekygirlsarah.com/blob/master/.ruby-version)中包含一个 `.ruby-version` [文件，告诉它要用什么版本来构建网站，它就会发挥它的魔力。](https://github.com/geekygirlsarah/geekygirlsarah.com/blob/master/.ruby-version)

它们甚至包括免费功能，比如从 Let ' s Encrypt 获取证书，这样我也可以免费使用 SSL。我的三个旧域名都在这里(geekygirlsarah.com，sarahwithee.com，geekygirlsarah.dev)。为了简单起见，它们都重定向到 geekygirlsarah.com。(我可能会在未来摆脱 sarahwithee.com，取而代之的是世界上另一个[威瑟斯](https://www.facebook.com/public/Sarah-Withee)。)

另一个很酷的特性是它们允许一些简单的 web 表单提交到某个地方。我在一个页面上配置了一个[联系表单](https://dev.to/contact/),让 Netlify 获取内容并通过电子邮件发送给我。它还可以设置垃圾邮件检测，这使得电子邮件保持苗条。免费版每月允许 100 条消息，但由于我从未用我上一个网站的垃圾邮件攻击过那个*，我并不太担心。*

但是最后，有一件讨厌的事情我想确保:向后兼容性。我仍然保留着名片、链接事物的旧推文、来自我的幻灯片的链接等等。我希望它们能够平滑无缝地过渡。Netlify 带着我的根里的一个`_redirects`文件再次来拯救我。这告诉他们只需将任何要访问左栏中 URL 的人发送到右栏中的 URL。这也让我可以做一些事情，比如把 T1 链接回它的新家 T2，但仍然可以工作。博客文章都转到它们原来的网址，它让两个网站的混乱变得像一个网站一样。

```
# Redirect domain aliases to primary domain
https://sarahwithee.com/* https://geekygirlsarah.com/:splat 301!
https://geekygirlsarah.dev/* https://geekygirlsarah.com/:splat 301!

# These extra rules are required because HTTPS is not forced
http://sarahwithee.com/* https://geekygirlsarah.com/:splat 301!
http://geekygirlsarah.dev/* https://geekygirlsarah.com/:splat 301!

# Optional: Redirect default Netlify subdomain to primary domain
https://geekygirlsarah.netlify.com/* https://geekygirlsarah.com/:splat 301!
http://geekygirlsarah.netlify.com/* https://geekygirlsarah.com/:splat 301!

# Misc captures
/talks/ /speaking/
/press/ /media/

# Talk - Building Your Team to Last
/build-your-team/ /speaking/building-your-team-to-last/
/building-your-team/ /speaking/building-your-team-to-last/
/buildingyourteam/ /speaking/building-your-team-to-last/
/buildyourteam/ /speaking/building-your-team-to-last/

# Other talks are included on full file... 
```

Enter fullscreen mode Exit fullscreen mode

我试着给我的演讲做简短的链接，我意识到它们可能很容易被混淆，所以我设置了人们可能会混淆的所有变体。(或者我可能忘了我跟人说过！)

## 分析学

Wordpress 包含了一些来自他们的 [Jetpack](https://jetpack.com/) 插件的很棒的分析。我用了，但是不能再用在静态网站上了。我知道我不想要谷歌分析(因为我试图避免把我的生活扔进他们渴求数据的服务器)，所以我寻找替代方案。我找到了 [Matomo](https://matomo.org/) ，他们的分析费用是 20 美元/月。它只是我添加到页面上的一个像素跟踪器。当我在试用它来看看我是否喜欢它时， [Netlify 实际上宣布了他们自己的分析](https://www.netlify.com/blog/2019/07/10/netlify-analytics-accurate-insights-without-performance-impacts/)，每月 5 美元。因为他们的是基于纯粹的服务器日志(无论如何都要收集)，所以不需要像素跟踪器。由于日志存在，它不会降低网站的速度。我报名试用了一下，很喜欢。

## 反馈

我花了几周的时间做基础工作，然后把它发给一些朋友征求意见。他们中的大多数人立刻喜欢上了它。在接下来的一个月左右的时间里，随着我重新编写页面，重新设计演讲页面等内容的布局，以及重新设计导航以简化一切，反馈不断得到改善。

现在呢？我挺满意的！只需要几分钟就可以打开我的回购协议，如果需要的话做一点小改动，然后提交，Netlify 会自动引入这些改动，并在 20 秒内发布一个网站。我几乎可以在任何有文本编辑器的地方写博客，然后把它推到我的`_posts`目录，它就会神奇地出现在网站上。我可以很容易地用`bundle`和`jekyll serve`运行一个本地副本，然后看到任何东西。这也让我很高兴，我可以在网上提供它，以防有人想使用我做过的任何相同的事情。

我也很想听听你的想法，尤其是如果你还记得我以前的网站。在 Twitter 上给我发一条推文，在我的联系页面上给我发一条信息，或者通过其他方式给我发一条短信。我很想听听你的想法！我也很乐意回答关于你是否看到任何你想放入你的网站的问题。

感谢阅读！

~莎拉