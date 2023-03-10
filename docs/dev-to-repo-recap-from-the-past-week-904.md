# 开发人员对上周的回购进行总结

> 原文：<https://dev.to/devteam/dev-to-repo-recap-from-the-past-week-904>

欢迎回到另一个回购回顾，我们涵盖了上周对 [dev.to 的仓库](https://github.com/thepracticaldev/dev.to)[iOS 回购](https://github.com/thepracticaldev/dev-ios)和[Android 回购](https://github.com/thepracticaldev/dev-android)的贡献。这一期涵盖 6 月 15 日至 6 月 21 日。

# 主回购

## 特性

*   [@ben](https://dev.to/ben) 增加了 iOS 推送通知的字幕。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加字幕到 ios 推送通知 #3170](https://github.com/thepracticaldev/dev.to/pull/3170) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-06-15T02:58:00Z">Jun 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3170)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

充分利用 ios 通知的空间

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3170)

*   我们现在有一个投票功能(封闭测试版)！请在本的帖子中阅读更多信息:

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## 关于这个新开发功能的反馈？

### 开发团队的 Ben Halpern 月 15 日 193 分钟阅读

#meta #changelog](/devteam/feedback-on-this-new-dev-feature-5cai)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Beta 民意测验功能(目前仅供管理员使用) #3176](https://github.com/thepracticaldev/dev.to/pull/3176) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-06-15T21:15:16Z">Jun 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3176)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这个 PR 包含了一些在 tweets 中包含投票的基本功能。它限制只有管理员可以包括投票，因为我认为我们想要确定的投票有更多的复杂性，大致如下:

*   一篇有民意调查的文章可能不应该允许编辑，因为这可能会损害民意调查。
*   投票应该只能嵌入到一篇文章中，以保持上下文一致。
*   这意味着从液体标签的角度来看，对上下文有更多的“意识”。
*   一个投票应该通过一些相当简单的用户界面来定义，这个界面回避了投票何时被“保存”的问题。

所以现在我把它做得更像是一个概念性的功能，我们可以在内部使用，但在我们回答完其余的问题之前，展示它会很有趣。

原谅我傻傻的占位文字: [![](img/e05d54af4d5ce7fec4301be08b36efe1.png)](https://camo.githubusercontent.com/99d2837335ea7dfc36ee26be2d4e936f3b62f1dd/68747470733a2f2f636c2e6c792f6237626630383666396430312f496d616765253230323031392d30362d31352532306174253230352e30382e3438253230504d2e706e67)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3176)

*   [@ben](https://dev.to/ben) 新增极简灯光主题！你可以在[的设置中打开它。谢谢你，本！](https://dev.to/settings/misc)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加极简灯光主题 #3179](https://github.com/thepracticaldev/dev.to/pull/3179) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-06-16T04:42:33Z">Jun 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3179)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

添加新的主题“极简之光”，与默认主题相比，它包括更多的整体白色和极简主义。

[![](img/5cd1ef1ab55ed4e5068812a652a9d055.png)](https://camo.githubusercontent.com/1fc3c3dc2f6745b4d89a3df7e6cdb754cd4d103d/68747470733a2f2f636c2e6c792f3038343631383166393236392f496d616765253230323031392d30362d3136253230617425323031322e34312e3238253230414d2e706e67)

还对`html_variants`后端做了一些调整。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3179)

*   多亏了 [@tvanblargan](https://dev.to/tvanblargan) ，DEV post embeds/Liquid 标签现在有了发布日期。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加日期链接液体标签 #3126](https://github.com/thepracticaldev/dev.to/pull/3126) 

    [![12vanblart avatar](img/1fe4ab4bcfb01224e3c584027788f8a6.png)](https://github.com/12vanblart) **[12vanblart](https://github.com/12vanblart)** posted on [<time datetime="2019-06-11T15:15:20Z">Jun 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3126)

    ## 这是什么类型的公关？(勾选所有适用选项)

    *   [ ]重构
    *   [x]功能
    *   [ ]错误修复
    *   [ ]文档更新

    ## 描述

    我注意到一个不一致的地方，发布日期不包括在开发人员帖子的流动链接中。

    **免责声明:**我对 ruby 一点都不熟悉，一直在努力让它运行测试(Windows 10 -无法安装 WSL)。我也无法在 Gitpod [(可能有帮助信息的 Twitter 帖子)](https://twitter.com/jankeromnes/status/1138410459818790912)中使用液体标签。

    ## 相关车票&单据

    不适用的

    ## 手机&桌面截图/录音(如有 UI 改动)

    不适用(参见上述关于 Gitpod 的评论)

    ## 添加到文档中？

    *   [ ]文档开发到
    *   [ ]自述文件
    *   [x]不需要文档

    [View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3126)
*   现在有一个组织液体标签嵌入您的组织！谢谢， [@ben](https://dev.to/ben) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加组织液标签 #3203](https://github.com/thepracticaldev/dev.to/pull/3203) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-06-17T17:19:22Z">Jun 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3203)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

[![](img/43516b81078e0bd3eea48fcef71144b9.png)](https://camo.githubusercontent.com/62831595905f1b08a7b3c2952114594869fd2fa3/68747470733a2f2f636c2e6c792f3039613131353633373930382f496d616765253230323031392d30362d31372532306174253230312e31382e3437253230504d2e706e67)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3203)

*   [@claramorgen](https://dev.to/claramorgen) 更新了通知上的时间戳，以显示帖子的发布日期，而不是通知的创建时间。谢谢你，克拉摩根！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新通知上的时间戳 #3076](https://github.com/thepracticaldev/dev.to/pull/3076) 

[![claracodes avatar](img/cc462b9acc487eaed732988937dc2a9d.png)](https://github.com/claracodes) **[claracodes](https://github.com/claracodes)** posted on [<time datetime="2019-06-08T11:31:30Z">Jun 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3076)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

我将 notificatios 中的时间戳更改为评论/文章创建的时间。在它显示通知创建时的时间戳之前，这是令人困惑的。我把它改成了文章和评论，其他所有的通知都没有时间标签。

## 相关车票&单据

修复#2997

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![men with watch](img/2e7450e71d97e5d610e58744e76a1f95.png)](https://camo.githubusercontent.com/e52f32c5a2426020f18f073070517d7e45234c55/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f336437394e5963676d6c536b784a6a6945662f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3076)

*   现在有一个列表面板，方便你管理你的列表。谢谢， [@mariocsee](https://dev.to/mariocsee) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 清单仪表盘 #3014](https://github.com/thepracticaldev/dev.to/pull/3014) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-05-31T18:41:58Z">May 31, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3014)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]功能

## 描述

供用户管理其列表的仪表板:

*   查看他们的所有列表(信息:组织、标题、日期、类别标签|操作:编辑)
*   显示列表数量，创建列表按钮，登录用户的信用数量(以及他们所在的组织)，购买信用按钮
*   显示的信息会根据选择的组织或用户进行调整

未来的 PRs:

*   按创建/删除日期对列表排序
*   过期列表的可视指示
*   直接从 Dashboard 中删除列表，而不必进入编辑页面
*   分析学
*   在编辑中反映列表的过期状态，以便明确何时重新发布/删除。
*   测试整个 Preact 列表端

## 相关车票&单据

#2825

## 手机&桌面截图/录音(如有 UI 改动)

[![2019-06-10 16 20 57](img/f93518b4954298f813750a48b2b49a46.png)](https://user-images.githubusercontent.com/13403332/59224185-cacaf080-8b9b-11e9-81ca-fa98cebfbdf4.gif)

[![2019-06-10 16 44 46](img/0af329fcf509753d34620d420d30fb58.png)](https://user-images.githubusercontent.com/13403332/59225611-2cd92500-8b9f-11e9-8c9c-fb8394b200af.gif)

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3014)

*   [@rhymes](https://dev.to/rhymes) 为 pro 会员增加了新的历史功能(目前已关闭的测试版程序)。详情请看公关。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 亲:历史 #3220](https://github.com/thepracticaldev/dev.to/pull/3220) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-06-18T11:17:58Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3220)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

在这个 PR 中，我们引入了一个只有专业用户才能使用的新功能:历史记录。

这个特性从阅读列表中借鉴了很多，也许将来我们可以重构两者来共享更多的代码(在几乎相同的 CSS、JS 和 Ruby 代码中)。我觉得这个公关应该不会马上这么做。

### 索引

至于阅读列表，我们使用 Algolia 进行索引。索引由`PageView`的实例构建(只有专业用户的实例被添加到索引中):

这是指数的定义:

```
  algoliasearch index_name: "UserHistory", per_environment: true, if: :belongs_to_pro_user? do
    attributes :referrer, :time_tracked_in_seconds, :user_agent, :article_tags

    attribute(:article_title) { article.title }
    attribute(:article_path) { article.path }
    attribute(:article_reading_time) { article.reading_time }
    attribute(:viewable_by) { user_id }
    attribute(:visited_at_timestamp) { created_at.to_i }

    attribute :article_user do
      # ...
    end

    attribute :readable_visited_at do
      # ...
    end

    searchableAttributes(
      %i[referrer user_agent article_title article_searchable_tags article_searchable_text],
    )

    tags { article_tags }

    attributesForFaceting ["filterOnly(viewable_by)"]

    customRanking ["desc(visited_at_timestamp)"]
  end
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们索引引用(目前未使用)，时间跟踪(目前未使用)，用户阿根(目前未使用)，文章的一些属性(标签，标题，路径，阅读时间，用户)。我们还索引了一个可读的日期(不是阅读列表中文章发表的时间，而是用户访问该文章的时间)。

我们还允许用户搜索文章的标签，标题和正文摘录。可搜索的索引目前包含了推荐人和用户代理，但是我不确定在没有更好的用户界面(或者一个可见的方面)的情况下，这些应该是“安静的”可搜索的吗？).

然后，所有内容都按照时间戳降序排列，这样新的访问就会出现在顶部

### 分页

此 PR 包含结果的分页，见下面的截图

#### 开放性问题/议题

*   如果我们不显示推荐人、追踪时间和用户代理，我们应该添加它们吗？如果我们真的想展示给他们看，最好的方法是什么？

*   用户应该能够“静默”地搜索推荐人和用户代理吗？

*   我们还应该向 UI 中的单个项目添加什么？

*   页面浏览量可以重复，如果我连续两次访问一篇文章，我会看到两个条目。可以吗？参见示例:

[![Screenshot 2019-06-18 at 1 15 25 PM](img/8b9e4a7c42de30820c6ce4a82229873e.png)](https://user-images.githubusercontent.com/146201/59677854-34b24e00-91cb-11e9-9272-b973a4f65cf5.png)

*   我们应该把历史页面的链接放在首页吗？

## 手机&桌面截图/录音(如有 UI 改动)

### 看起来怎么样

[![Screenshot_2019-06-18 History - The DEV(local) Community](img/416befec2490489e2f5871e99f53b92d.png)](https://user-images.githubusercontent.com/146201/59677890-4c89d200-91cb-11e9-8699-4af5a938d399.png)

### 分页和搜索演示

[![history](img/abec2eac046f073bb5fe0b3f27dfec17.png)](https://user-images.githubusercontent.com/146201/59677911-56abd080-91cb-11e9-9f40-ae8e0ccb33b1.gif)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3220)

*   @韵脚在仪表盘中为职业会员增加了一个“统计”按钮。再次感谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在仪表盘中添加文章职业统计按钮 #3230](https://github.com/thepracticaldev/dev.to/pull/3230) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-06-19T08:39:26Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3230)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

如果当前用户是专业用户，则在文章的仪表板中显示统计按钮

## 手机&桌面截图/录音(如有 UI 改动)

[![Screenshot_2019-06-19 Dashboard - DEV(local) Community 👩‍💻👨‍💻](img/86fa4aed79e3bc4ecaa235b2cd90693b.png)](https://user-images.githubusercontent.com/146201/59750046-794af180-927e-11e9-8f17-ad17ebbe6bdf.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3230)

*   [@jess](https://dev.to/jess) 增加了一个内部特性，将某个东西标记为缓冲。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 给个别文章添加缓冲复选框 #3238](https://github.com/thepracticaldev/dev.to/pull/3238) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-06-19T18:28:24Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3238)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

*   让管理员可以选择将文章标记为缓冲，即使它没有通过通常的缓冲流。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3238)

*   [@bellsenawat](https://dev.to/bellsenawat) 为阅读列表增加了一个“加载更多”按钮。谢谢@bellensenawat！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 功能/阅读列表加载更多按钮 #3221](https://github.com/thepracticaldev/dev.to/pull/3221) 

[![bellsenawat avatar](img/bb2a86694bda4cb0cdf4f18d21423202.png)](https://github.com/bellsenawat) **[bellsenawat](https://github.com/bellsenawat)** posted on [<time datetime="2019-06-18T11:33:32Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3221)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

当读取列表的数组长度超过 64 时，会显示一个“加载更多”按钮，当没有要从 API 加载的读取列表时，该按钮会隐藏。

## 相关车票&单据

#3122

## 手机&桌面截图/录音(如有 UI 改动)

[![dev_to_ss](img/e230fa2551dfabc8ecad837321d4ff13.png)](https://user-images.githubusercontent.com/5281760/59678525-dbf8aa80-91f6-11e9-85f2-14e43551a561.gif)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3221)

## Bug 修复/其他贡献

*   [@mazentouati](https://dev.to/mazentouati) 为 dev.to repo 的 Windows 安装添加一个可能的错误解决方案。谢谢，[@ mazenouati](https://dev.to/mazentouati)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 为 windows 安装添加一个可能的错误解决方案 #3178](https://github.com/thepracticaldev/dev.to/pull/3178) 

[![sunchayn avatar](img/13382d0f552c9cb36d59c69df040b454.png)](https://github.com/sunchayn) **[sunchayn](https://github.com/sunchayn)** posted on [<time datetime="2019-06-16T03:37:05Z">Jun 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3178)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

我在安装 DEV 时遇到了一个未记录的错误:`ERROR: Error installing pg`。此 PR 增加了所需的分辨率。

## 添加到文档中？

*   [x]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3178)

*   [@韵脚](https://dev.to/rhymes)为`PollVote`模型移除了一个额外的`presence`验证器。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除 PollVote   #3187](https://github.com/thepracticaldev/dev.to/pull/3187) 中的双重存在验证器

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-06-17T11:07:00Z">Jun 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3187)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

在`PollVote`中有一个双`presence: true`验证器

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3187)

*   添加了一个开发设置，如果加载了太多记录，它会发出警告。再次感谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在开发中添加 warn _ on _ records _ fetched _ greater _ than 设置【跳过 ci】# 3182](https://github.com/thepracticaldev/dev.to/pull/3182)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-06-16T10:37:43Z">Jun 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3182)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

这应该有助于注意在单个查询中意外加载太多记录的情况。该评论摘自[https://guides.rubyonrails.org/configuring.html](https://guides.rubyonrails.org/configuring.html)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3182)

*   [@briwa](https://dev.to/briwa) 修复了`<hr>`标签在黑暗主题中的外观。谢谢，[@布里瓦](https://dev.to/briwa)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复(文章):`< hr >`应该在夜晚主题  #3202](https://github.com/thepracticaldev/dev.to/pull/3202) 中可见

[![briwa avatar](img/615a01ed7850848e0c89fefa1cd6c0a0.png)](https://github.com/briwa) **[briwa](https://github.com/briwa)** posted on [<time datetime="2019-06-17T15:59:37Z">Jun 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3202)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

我今天刚刚尝试了夜间模式(我知道，我很慢...<g-emoji class="g-emoji" alias="yum" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f60b.png">😋</g-emoji>)我意识到`<hr/>`元素并不是那么明显:

[![Screen Shot 2019-06-17 at 11 49 45 PM](img/68e558433ffc0b5c25fd61e24055f59d.png)](https://user-images.githubusercontent.com/8046636/59618468-dfd6ef00-915a-11e9-9b10-2b9b31ac37f9.png)

稍微对比一下默认/极简淡/粉色主题: [![Screen Shot 2019-06-17 at 11 46 51 PM](img/9769a397e4a0d4eff21931d50b3b323b.png)](https://user-images.githubusercontent.com/8046636/59619057-309b1780-915c-11e9-831a-b46baf1ee070.png)

### 提议

我建议把它改成这样:

[![Screen Shot 2019-06-17 at 11 46 29 PM](img/f4831bba6a2b57e6a550d7d2ac15a46a.png)](https://user-images.githubusercontent.com/8046636/59618485-eb2a1a80-915a-11e9-9595-f1439877570e.png)

以反映夜晚的主题。它不应该改变默认/最小光/粉红色主题的任何东西，因为它不是背景的对比。

我不太确定我用`themeable`做的事情是否正确，所以请告诉我。还有如果有更好的颜色建议。谢谢你。

## 相关车票&单据

*   没有人

## 手机&桌面截图/录音(如有 UI 改动)

*   没有人

## 添加到文档中？

*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![](img/62e2cdf1d6a8d4a08237d15ec1bfecfe.png)](https://camo.githubusercontent.com/045d65b7ef08c072ab4ce1d3d4d7348c3f51fb74/68747470733a2f2f6d65646961322e67697068792e636f6d2f6d656469612f6d74396274464a37706741544b2f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3202)

*   [@mazentouati](https://dev.to/mazentouati) 修复了我们`Dockerfile`中的一些错别字。谢谢，[@ mazenouati](https://dev.to/mazentouati)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复 Dockerfile 评论中的一些错别字 #3214](https://github.com/thepracticaldev/dev.to/pull/3214) 

[![sunchayn avatar](img/13382d0f552c9cb36d59c69df040b454.png)](https://github.com/sunchayn) **[sunchayn](https://github.com/sunchayn)** posted on [<time datetime="2019-06-17T19:20:21Z">Jun 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3214)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

修正 Dockerfile 评论中的一些错别字

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3214)

*   @lightalloy 修复了 Slack 消息被发送到错误频道名称的问题。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新松弛频道名称 #3218](https://github.com/thepracticaldev/dev.to/pull/3218) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-06-18T09:57:52Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3218)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

松弛信道`warned-user-activity`被重命名为`warned-user-comments`但不在代码中，因此`SlackBot`无法向该信道发送消息，从而导致错误`The slack API returned an error: channel_not_found (HTTP Code 404)`

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3218)

*   @lightalloy 还修复了 Twitch webhook 返回空数据的错误处理。再次感谢你，安娜！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)twitch 返回空数据时不要尝试注册 web hook# 3219](https://github.com/thepracticaldev/dev.to/pull/3219)

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-06-18T10:19:14Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3219)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

我在#2865 中修复了 twitch 没有返回数据的错误。有时 twitch 会返回空数据，我们也应该处理这种情况。

## 相关车票&单据

#2784 #2865

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3219)

*   [@mazentouati](https://dev.to/mazentouati) 修复了堆栈溢出配置文件 URL 中不允许堆栈溢出子社区的问题。谢谢，[@ mazenouati](https://dev.to/mazentouati)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 允许 stackoverflow 子社区简介 #3215](https://github.com/thepracticaldev/dev.to/pull/3215) 

[![sunchayn avatar](img/13382d0f552c9cb36d59c69df040b454.png)](https://github.com/sunchayn) **[sunchayn](https://github.com/sunchayn)** posted on [<time datetime="2019-06-18T03:17:40Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3215)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

堆栈溢出有 4 个子社区:西班牙语，俄语，日语和葡萄牙语。这种公关将有可能链接到这些社区之一的个人资料。

## 相关车票&单据

修复#3204

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3215)

*   [@lightalloy](https://dev.to/lightalloy) 修复了在验证空实例时对`Mention`模型的验证。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复验证空实例时提及验证 #3188](https://github.com/thepracticaldev/dev.to/pull/3188) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-06-17T13:55:16Z">Jun 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3188)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

*   修正了`undefined method for Nilclass`上的`Mention.new.valid?`失败
*   使用工厂时创建有效的提及

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3188)

*   [@cyrillefr](https://dev.to/cyrillefr) 将`HtmlVariantSuccess.create` `DelayedJob`通话转移到`ActiveJob`。谢谢， [@cyrillefr](https://dev.to/cyrillefr) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移动 HtmlVariantSuccess.create 延迟调用 ActiveJob  #3173](https://github.com/thepracticaldev/dev.to/pull/3173) 

[![cyrillefr avatar](img/7ef3538029145d4cf304bfb0f0cca6f5.png)](https://github.com/cyrillefr) **[cyrillefr](https://github.com/cyrillefr)** posted on [<time datetime="2019-06-15T15:57:15Z">Jun 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3173)

*   添加 AJ 职务+职务说明
*   重构调用

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将 HtmlVariantSuccess.create 延迟调用移动到 ActiveJob

## 相关车票&单据

#3136

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3173)

*   [@rhymes](https://dev.to/rhymes) 对列表面板做了一些优化。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 仪表盘列表:优化查询 #3233](https://github.com/thepracticaldev/dev.to/pull/3233) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-06-19T11:01:36Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3233)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

现在我们两次调用同一个查询，而我们可以只调用一次，甚至避免创建立即被丢弃的 Ruby 对象

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3233)

*   修正了一个组织帖子不能正确显示统计数据的错误。再次感谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 显示组织统计文章 #3232](https://github.com/thepracticaldev/dev.to/pull/3232) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-06-19T09:30:13Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3232)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

有一个错误，前端没有将组织 id 传递给后端，因此获取组织文章的统计数据失败。

这也为`fetch` API 增加了一点错误处理

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3232)

*   [@ben](https://dev.to/ben) 更新了一个硬编码的图像，改为使用 Rails 资产管道的相对路径。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 从聊天频道图像中移除硬编码图像 #3235](https://github.com/thepracticaldev/dev.to/pull/3235) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-06-19T13:46:48Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3235)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

切换出硬编码图像用于文件的动态引用

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3235)

*   [@jess](https://dev.to/jess) 为一个列表的 OpenGraph 社交卡添加了列表标志。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 为社会卡添加缺失房源标识 #3237](https://github.com/thepracticaldev/dev.to/pull/3237) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-06-19T15:36:18Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3237)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

*   上周错过了一个文件，这增加了上市标志上市社会卡。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3237)

*   @mariocsee 修复了作为组织管理员管理列表的问题。谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 作为组织管理员管理物品 #3228](https://github.com/thepracticaldev/dev.to/pull/3228) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-06-18T21:52:07Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3228)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

在新推出的列表面板中(见下面的 PR)，如果您是一个或多个组织的管理员，您将看到这些组织下的所有列表。它还显示了每个列表的 edit 按钮，但是当您单击 edit 时，它会导致一个错误，因为授权仅限于列表中具有相同 user_id 的用户，而没有考虑想要编辑列表的 org_admins。

此 PR 在适当的列表中添加了 org _ 的 org_admins 验证。

## 相关车票&单据

#3014 列表仪表板 PR

## 手机&桌面截图/录音(如有 UI 改动)

不适用的

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3228)

*   [@maestromac](https://dev.to/maestromac) 更新了 Windows 安装文档中的`statement_timeout`错误。谢谢你，麦克！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新单据对帐单 _ 超时错误 #3248](https://github.com/thepracticaldev/dev.to/pull/3248) 

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2019-06-20T17:30:56Z">Jun 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3248)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]文档更新

## 相关车票&单据

[https://github.com/thepracticaldev/dev.to/issues/3180](https://github.com/thepracticaldev/dev.to/issues/3180)

## 添加到文档中？

*   [x]文档开发到

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3248)

*   [@maestromac](https://dev.to/maestromac) 也更新了`RssReader`以正确处理液体和 YouTube 标签。再次感谢你，麦克！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新 RssReader 的液态& youtube 解析逻辑 #3229](https://github.com/thepracticaldev/dev.to/pull/3229) 

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2019-06-18T21:52:36Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3229)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

*   前转义`{{ }}`用反勾号分隔 MarkdownParser 的表达式。
*   放松 RssReader 的 youtube id 解析逻辑。

## 相关车票&单据

[https://github.com/thepracticaldev/dev.to/issues/3070](https://github.com/thepracticaldev/dev.to/issues/3070)

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3229)

*   我修复了一个错误，删除一个评论仍然允许评论的`title`属性可见。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除评论标题如果被删除 #3249](https://github.com/thepracticaldev/dev.to/pull/3249) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-06-20T19:15:43Z">Jun 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3249)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

这修正了一个错误，即评论的`title`属性返回一些评论的文本，即使它被删除了。

还修正了通知卡的一个小错误，整个通知卡`<div>`显示指针光标。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3249)

*   [@cyrillefr](https://dev.to/cyrillefr) 将`HtmlVariantTrial.create` `DelayedJob`通话转移到`ActiveJob`。谢谢， [@cyrillefr](https://dev.to/cyrillefr) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将 HtmlVariantTrial.create 延迟调用移动到 active job(# 3173)# 3239](https://github.com/thepracticaldev/dev.to/pull/3239)

[![cyrillefr avatar](img/7ef3538029145d4cf304bfb0f0cca6f5.png)](https://github.com/cyrillefr) **[cyrillefr](https://github.com/cyrillefr)** posted on [<time datetime="2019-06-19T18:57:45Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3239)

```
- add AJ job + job spec
- refactor call 
```

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将 HtmlVariantTrial.create 延迟调用移动到 ActiveJob

## 相关车票&单据

#3136

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3239)

*   [@ben](https://dev.to/ben) 修复了一些逻辑，调整了奖励 tag 徽章的副本。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修正逻辑，调整副本以获得标签奖励 #3251](https://github.com/thepracticaldev/dev.to/pull/3251) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-06-20T21:39:54Z">Jun 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3251)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

修正了标签徽章奖励者没有计入上周文章的错误

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3251)

*   [@lightalloy](https://dev.to/lightalloy) 修复了一些 Rubocop(一个 Ruby/Rails linter)的问题，并创建了一个新的`.rubocop_todo.yml`。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 固定 rubocop 问题，新。ruboop _ todo . yml# 3255](https://github.com/thepracticaldev/dev.to/pull/3255)

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-06-21T08:49:15Z">Jun 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3255)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   修正了新的机械战警问题
*   生成了新的`.rubocop_todo.yml`

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3255)

*   [@rhymes](https://dev.to/rhymes) 删除了一些已经废弃的指导功能的死代码。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除代码为遗留师徒功能 #3186](https://github.com/thepracticaldev/dev.to/pull/3186) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-06-17T10:53:55Z">Jun 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3186)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这将删除“遗留”的指导功能代码。它还删除了`users`表和`mentor_relationships`表中的列。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3186)

## 新的问题和讨论

*   [@kensixx](https://dev.to/kensixx) 请求了一个功能，其中“返回关注”按钮显示在关注者仪表板中。谢谢， [@kensixx](https://dev.to/kensixx) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 列表中每个关注者的“关注返回”按钮如果你还没有关注到他们 #3169](https://github.com/thepracticaldev/dev.to/issues/3169) 

[![kensixx avatar](img/c91d75b2ff6ec34955e758f821be7af6.png)](https://github.com/kensixx) **[kensixx](https://github.com/kensixx)** posted on [<time datetime="2019-06-15T02:20:08Z">Jun 15, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3169)

**您的功能请求是否与某个问题相关？请描述一下。**

我刚刚注意到，当你查看你的关注者列表时，关注者的名字旁边没有“返回关注”选项。

现在，你必须点击追随者的个人资料，然后你就可以跟踪他/她的背影了。

**描述您想要的解决方案**

如果在关注者的名字旁边有一个“返回关注”按钮，或者在每个分区(如果是分区)的最右边(如果你目前还没有被关注到他/她),那就更好了。对我来说，这使得跟踪更快更好，我猜=)

如果你已经被他/她关注了，我想会出现一个“被关注”的图标。=)

添加了一张图片来展示我目前的想法，我在每个关注者的末尾添加了蓝色方块=)

[![image](img/00bc06609280a82192970203ed17d084.png)](https://user-images.githubusercontent.com/24205790/59545795-aa61b400-8f56-11e9-8926-fa39086801c5.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3169)

*   [@jsjoeio](https://dev.to/jsjoeio) 请求了一个功能来关闭他们关注的人的通知。谢谢， [@jsjoeio](https://dev.to/jsjoeio) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 功能请求:关闭我关注的人的通知 #3171](https://github.com/thepracticaldev/dev.to/issues/3171) 

[![jsjoeio avatar](img/4375d71830d03839587d2e33f617d112.png)](https://github.com/jsjoeio) **[jsjoeio](https://github.com/jsjoeio)** posted on [<time datetime="2019-06-15T14:22:48Z">Jun 15, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3171)

**您的功能请求是否与某个问题相关？请描述一下。**

当我看到与我关注的人创建新帖子相关的通知时，我很沮丧。

**描述您想要的解决方案**

我希望能够关闭通知，这样当我关注的人创建新帖子时，我就不会收到通知。

我不确定解决这个问题的最佳方法，但有两个选择可能会很好:

1.  关闭我关注的人的帖子的所有通知
2.  有一个我关注的人的列表，并且能够有选择地选择我想要通知他们新帖子的人。

这样，只有当人们与我的帖子互动时，我才会收到通知。**描述你考虑过的替代方案**

当前变通办法:

*   不要跟踪任何人
*   不要打开通知

(好吧，这些是为了搞笑，但我还没有看到任何真正的替代品)。

**附加上下文**

我没有时间自己去做(或者体验，对我来说还没有 Ruby exp)。

Twitter 上的其他人似乎也同意这将是一件好事。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3171)

*   [@bhupesh](https://dev.to/bhupesh) 请求了一个特性:`<meta>`标签，包括一篇文章的标签。谢谢， [@bhupesh](https://dev.to/bhupesh) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在元标签  #3181](https://github.com/thepracticaldev/dev.to/issues/3181) 中添加开发标签

[![Bhupesh-V avatar](img/ee5f493a912a2c334f3d478a58daef3e.png)](https://github.com/Bhupesh-V) **[Bhupesh-V](https://github.com/Bhupesh-V)** posted on [<time datetime="2019-06-16T07:52:30Z">Jun 16, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3181)

**附加上下文**

在元信息中包含`article:tag`对 SEO 来说是个好主意，我已经看到 dev.to 已经有了一个`og:type`标签。

```
<meta property="og:type" content="article">
```

Enter fullscreen mode Exit fullscreen mode

所以加入这个可能是个好主意。freecodecamp 也有一个`article:tag`

```
<meta property="article:tag" content="Angular">
```

Enter fullscreen mode Exit fullscreen mode

进一步说，还可以加上`article:author`。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3181)

*   @avalander 请求了一个编辑可以显示标签指南的功能。谢谢， [@avalander](https://dev.to/avalander) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 特性请求:列表编辑器中添加了标签指南 #3216](https://github.com/thepracticaldev/dev.to/issues/3216) 

[![Avalander avatar](img/845f2eb7f357641a6b489a5cdd8583f6.png)](https://github.com/Avalander) **[Avalander](https://github.com/Avalander)** posted on [<time datetime="2019-06-18T08:45:17Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3216)

**您的功能请求是否与某个问题相关？请描述一下。**没什么问题，但是我注意到有些标签被滥用了很多次。尤其是`discuss`标签似乎被用作各种主题的通配符。由于指南只在标签页上可见，我怀疑很多用户甚至不知道它们的存在。

**描述您想要的解决方案**在编辑器视图中显示添加到帖子中的标签的准则，并警告不符合准则的标签可能会被删除，这可能会很好。

**描述你考虑过的替代方案**

**附加语境** [![Screenshot_20190618_104322](img/86af8434cf30e28d14a211a04358b1b3.png)](https://user-images.githubusercontent.com/9766906/59666840-ef375600-91b5-11e9-9c71-e7e7eb0c739e.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3216)

*   [@rugk](https://dev.to/rugk) 请求通过浏览器的`prefers-color-scheme`设置自动启用/禁用黑暗模式的功能。谢谢，[@ rugg](https://dev.to/rugk)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 通过首选配色  #3222](https://github.com/thepracticaldev/dev.to/issues/3222) 自动启用/禁用黑暗模式

[![rugk avatar](img/15c238948ca3753e0d27662f89aface7.png)](https://github.com/rugk) **[rugk](https://github.com/rugk)** posted on [<time datetime="2019-06-18T15:04:59Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3222)

**您的功能请求是否与某个问题相关？请描述一下。**

有一个黑暗模式的手动开关，但这很不方便，如果每个站点都这样做，我必须在每个站点上都这样做。

**描述您想要的解决方案**

有一个相当新的 CSS 媒体查询特性叫做 [`prefers-color-scheme`](https://developer.mozilla.org/docs/Web/CSS/@media/prefers-color-scheme) 。它可以让你检测系统/用户是否想要一个黑暗的网站，这样你就可以调整你的 CSS。

目前只有 Safari 和 Firefox 67 或更高版本支持它。

无论如何，如果你能利用这一点来自动选择正确的风格，那就太好了。

**描述你考虑过的替代方案**

保持手动开关->难看。(注意:您仍然可以保留它，因此用户可以覆盖它，但是您可以更改默认值。)

**附加上下文**

顺便说一句:我还有一个火狐插件叫做“黑暗模式网站切换器” ( [源代码](https://github.com/rugk/website-dark-mode-switcher))，你可以用它直接在火狐中切换这个设置——不需要改变系统选项。也许那会有帮助。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3222)

*   @karaluton 申请了一个功能，会员可以在他们的个人资料上添加 Instagram 链接。谢谢，[@卡拉路顿](https://dev.to/karaluton)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Instagram 链接 #3224](https://github.com/thepracticaldev/dev.to/issues/3224) 

[![karaluton avatar](img/f5bc686859ec6a8ab0fd11845f38fdbf.png)](https://github.com/karaluton) **[karaluton](https://github.com/karaluton)** posted on [<time datetime="2019-06-18T15:59:19Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3224)

我想有一个 Instagram 链接添加。Instagram 上有一个不断增长的开发者社区，能够从我们的 dev.to 个人资料链接到 Instagram 会很好。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3224)

*   [@taillogs](https://dev.to/taillogs) 报告了反应通知计数的问题。Thansk， [@taillogs](https://dev.to/taillogs) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 报道“近期反应”说不通 #3225](https://github.com/thepracticaldev/dev.to/issues/3225) 

[![rylandg avatar](img/8ad5dc681ea09a1afa57530c2c52f033.png)](https://github.com/rylandg) **[rylandg](https://github.com/rylandg)** posted on [<time datetime="2019-06-18T16:17:06Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3225)

**描述 bug** 我的通知窗口有时会显示“90”人最近做出了反应，但几分钟后就会看到“70”人最近做出了反应。这非常令人困惑，并且不会增加体验的价值。

**转载**在一个帖子上得到很多反应。在通知窗口中观察更新。

**预期行为**我假设这是通过通知指标的滚动窗口实现的。这是完全正确的，但也许通知窗口可以显示最近反应的时间范围。

“70 人在过去的 X 分钟内作出反应”或只显示总反应！

**桌面(请填写以下信息):**

*   OS: OSX
*   浏览器:铬

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3225)

*   [@taillogs](https://dev.to/taillogs) 报告了图片上传按钮在特定屏幕宽度下消失的问题。再次感谢， [@taillogs](https://dev.to/taillogs) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 图片上传元素在特定浏览器比例下丢失 #3226](https://github.com/thepracticaldev/dev.to/issues/3226) 

[![rylandg avatar](img/8ad5dc681ea09a1afa57530c2c52f033.png)](https://github.com/rylandg) **[rylandg](https://github.com/rylandg)** posted on [<time datetime="2019-06-18T16:25:19Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3226)

**描述错误**当 Dev.to 窗口的水平尺寸缩小时,“图像”按钮可能会丢失，必须调整窗口大小才能恢复。

**重现**

1.  编辑帖子。
2.  观察编辑器窗格中的图像按钮时，水平缩小浏览器窗口
3.  等到它消失

**预期行为**按钮不会在浏览器之外进行精神旅行。

**截图**之前

[![Screen Shot 2019-06-18 at 9 24 45 AM](img/0b19f504960792acabff68345b94800c.png)](https://user-images.githubusercontent.com/27736122/59701673-f9a02280-91aa-11e9-8d11-9c9a839734de.png)

在...之后

[![Screen Shot 2019-06-18 at 9 23 26 AM](img/404f95b6130f967e1f8c2ac5c845290a.png)](https://user-images.githubusercontent.com/27736122/59701585-cb224780-91aa-11e9-8997-46abd95f1111.png)

**桌面(请填写以下信息):**

*   OS: OSX
*   浏览器:铬
*   版本:74.0.3729.169

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3226)

*   [@taillogs](https://dev.to/taillogs) 请求一个功能一次添加多张图片。再次感谢， [@taillogs](https://dev.to/taillogs) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 一次添加多幅图像 #3227](https://github.com/thepracticaldev/dev.to/issues/3227) 

[![rylandg avatar](img/8ad5dc681ea09a1afa57530c2c52f033.png)](https://github.com/rylandg) **[rylandg](https://github.com/rylandg)** posted on [<time datetime="2019-06-18T21:35:33Z">Jun 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3227)

**您的功能请求是否与某个问题相关？请描述一下。**您目前不能一次添加超过 1 张图像。这是图片上传过程中的一个问题，也是它非常乏味的原因。

**描述您想要的解决方案**允许我一次性上传所有图像。为 URI 生成连续的 uuids，因此可寻址性不是问题。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3227)

*   [@georgecoldham](https://dev.to/georgecoldham) 报告了一个错误，如果有人更改用户名，通知没有更新的链接。谢谢， [@georgecoldham](https://dev.to/georgecoldham) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 通知不更新名称改变的永久链接 #3234](https://github.com/thepracticaldev/dev.to/issues/3234) 

[![GeorgeColdham avatar](img/f100be2061f0cd9a3dd9be414f7c452b.png)](https://github.com/GeorgeColdham) **[GeorgeColdham](https://github.com/GeorgeColdham)** posted on [<time datetime="2019-06-19T11:48:28Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3234)

**描述 bug** 当收到通知时，文章/评论的链接使用的是可以用来引用它的同一个永久链接。如果一个用户改变了他们的名字，这个永久链接就会被破坏并导致 404 页面。

**重现**

1.  以某种方式生成通知
2.  更改通知发送者的用户名
3.  尝试跟随链接

**预期行为**链接应该仍然有效，并指向文章或评论。

**截图**通知带网址 [![Screenshot 2019-06-19 at 12 42 19](img/7e640d2c2b38f529c5aa2d43efb00282.png)](https://user-images.githubusercontent.com/21260083/59762895-3265f780-9290-11e9-8ef3-c99dc4c65cde.png) 以下通知 [![Screenshot 2019-06-19 at 12 45 16](img/917161e8c409f25e83895e72fda5aa67.png)](https://user-images.githubusercontent.com/21260083/59762899-32fe8e00-9290-11e9-9dc8-8aac75dfa97c.png) 评论文章。注:用户名更改 [![Screenshot 2019-06-19 at 12 44 29](img/30456568f92725ccf9fb771fa80b6f92.png)](https://user-images.githubusercontent.com/21260083/59762896-3265f780-9290-11e9-9672-094b335a1d6b.png) 名称更改后评论的永久链接 URL[![Screenshot 2019-06-19 at 12 44 50](img/d8d2808d39dd09dde92959d4541e3993.png)](https://user-images.githubusercontent.com/21260083/59762898-32fe8e00-9290-11e9-9d56-e15cdbf71a4e.png)

**桌面(请填写以下信息):**

*   操作系统:Mac OS Mojave 10.14.5
*   浏览器:谷歌浏览器
*   版本:75

**建议**我的建议是，将账户与一个唯一生成的 id 关联起来，允许独立于任何永久链接来更改名称。用户名可以重定向到 url 的唯一 id 版本。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3234)

*   [@彼得](https://dev.to/peter)请求我们在评论中制作具有属性`rel="nofollow"`的链接。谢谢彼得。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在评论中制作链接 rel = " no follow "# 3236](https://github.com/thepracticaldev/dev.to/issues/3236)

[![pkfrank avatar](img/a6281578f8aad0af41d10413c0790fd2.png)](https://github.com/pkfrank) **[pkfrank](https://github.com/pkfrank)** posted on [<time datetime="2019-06-19T14:16:14Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3236)

**您的功能请求是否与某个问题相关？请描述一下。**有时，垃圾邮件用户会加入 DEV，以便发布主要为反向链接设计的文章/评论。我们已经有了一些机制来主动和被动地处理这种滥用，但我们可以做更多的工作来专门阻止垃圾评论。

**描述您想要的解决方案**在评论中制作所有链接`rel="nofollow"`。

**附加上下文**根据与团队的会议，这将意味着在解析评论中的降价时将`rel="nofollow"`添加到链接中，**但不在文章中**。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3236)

*   [@michaeltharrington](https://dev.to/michaeltharrington) 报告了一个 HTML 元素在通知框区域不转义的问题。谢谢你，迈克尔！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  HTML 元素在活动视图中没有被转义 #3241](https://github.com/thepracticaldev/dev.to/issues/3241) 

[![michael-tharrington avatar](img/c934bfd77e2c4ba8316feb7132acb1de.png)](https://github.com/michael-tharrington) **[michael-tharrington](https://github.com/michael-tharrington)** posted on [<time datetime="2019-06-19T20:53:58Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3241)

**描述 bug** 在 e-[https://dev . to/rikschennik/ul-or-ol-do-you-know-When-to-choose-which-5 CIC](https://dev.to/rikschennink/ul-or-ol-do-you-know-when-to-choose-which-5cic)上发布这样一篇标题中有 HTML 元素的文章时，这些元素将无法在通知页面上正确显示-[https://dev . to/rikschennik/ul-or-ol-do-you-know-When-to-choose-which-5 CIC](https://dev.to/notifications)。

当 HTML 元素被放在网站上所有视图的标题中时，它们应该显示为文本。

**截图**

[![https://cl.ly/0ba5754e8c9b/download/Image%202019-06-19%20at%204.53.07%20PM.png](img/a4f57b0c98e64a437b0c8a11b398be6a.png)](https://camo.githubusercontent.com/0dd37b6167dc3e3b3c404de90bd5d91575ffc6a2/68747470733a2f2f636c2e6c792f3062613537353465386339622f646f776e6c6f61642f496d616765253230323031392d30362d31392532306174253230342e35332e3037253230504d2e706e67)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3241)

*   [@taillogs](https://dev.to/taillogs) 报告了一个问题，在`/comments`视图的摘要中，将代码块作为帖子的第一个内容看起来很奇怪。谢谢， [@taillogs](https://dev.to/taillogs) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 代码块作为帖子的第一个内容导致预览效果不佳 #3242](https://github.com/thepracticaldev/dev.to/issues/3242) 

[![rylandg avatar](img/8ad5dc681ea09a1afa57530c2c52f033.png)](https://github.com/rylandg) **[rylandg](https://github.com/rylandg)** posted on [<time datetime="2019-06-20T01:12:20Z">Jun 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3242)

**描述 bug** 如果你的帖子以一个代码块开始，预览显示它没有格式，也没有`\n`。

哦，我注意到，在发布这个的时候，一个帖子的第一部分的图片链接也不会转移到预览。

**转载**示例帖子

```
```
// caller
function foo(callback) {
  callback('world');
}

// callback function
function myCallback(name) {
  console.log(`Hello ${name}`); // "hello world"
}

// pass callback to caller
foo(myCallback);
``` 
```

创建草稿并查看预览

**预期行为**暂时删除预览。也许像 backlog 这样聪明的东西。

**截图** [![Screen Shot 2019-06-19 at 6 10 20 PM](img/cad8936b4b53431905903eb28f30f266.png)](https://user-images.githubusercontent.com/27736122/59811183-905d0400-92bd-11e9-9349-bdb5532ef709.png)

**桌面(请填写以下信息):**

*   OS: OSX
*   浏览器:铬

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3242)

*   [@emma](https://dev.to/emma) 请求了一个关闭特定帖子反应通知的功能。谢谢，[@艾玛](https://dev.to/emma)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 关闭红心通知 #3244](https://github.com/thepracticaldev/dev.to/issues/3244) 

[![emgoto avatar](img/f7f9a2b631159ff1715dbe9b79a496b6.png)](https://github.com/emgoto) **[emgoto](https://github.com/emgoto)** posted on [<time datetime="2019-06-20T05:37:35Z">Jun 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3244)

**您的功能请求是否与某个问题相关？请描述一下。**当我收到评论或文章的心形/赞时，我想关闭通知功能(通过右上角的数字图标)。

**描述您想要的解决方案**

当您进入通知设置页面时，有一个复选框选项可以关闭接收红心/喜欢的通知。理想情况下，我希望在有人评论/回复评论时得到通知，而不是其他。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3244)

*   [@drewswaycool](https://dev.to/drewswaycool) 请求了一个排除标签的功能

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 排除标签 #3246](https://github.com/thepracticaldev/dev.to/issues/3246) 

[![drewswaycool avatar](img/848b2be4ec161309eb7133b394a7f24a.png)](https://github.com/drewswaycool) **[drewswaycool](https://github.com/drewswaycool)** posted on [<time datetime="2019-06-20T16:28:58Z">Jun 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3246)

我希望能够看到所有#react 标签帖子，但不包括#react #redux 帖子。

如果总是根据你不想看到的标签来排除帖子就好了。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3246)

*   @michaeltharrington 请求一个功能，允许人们暂时隐藏他们的帐户，或者进入休假模式。谢谢你，迈克尔！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 让用户能够暂时隐藏自己的账户(休假模式) #3252](https://github.com/thepracticaldev/dev.to/issues/3252) 

[![michael-tharrington avatar](img/c934bfd77e2c4ba8316feb7132acb1de.png)](https://github.com/michael-tharrington) **[michael-tharrington](https://github.com/michael-tharrington)** posted on [<time datetime="2019-06-20T22:23:27Z">Jun 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3252)

**您的功能请求是否与某个问题相关？请描述一下。**

用户偶尔希望能够暂时隐藏/停用他们的帐户，以便他们能够在 DEV 上保留他们的内容和历史，但如果需要，可以因工作/其他在线承诺而休息一下。

**描述您想要的解决方案**

也许用户可以打开“度假”模式，这样他们的账户就看不见了。在此模式下:

*   用户将无法通过 Connect 发表评论/使用反应/写帖子/列表/消息(也许其他动作？)

*   他们的文字、个人资料和评论都是不可见的。(任何可以识别活动的东西)

*   他们仍然可以登录以查看他们的订阅源，向他们的阅读列表添加项目，在他们的个人资料不可见时更改其内容，等等。

*   通知仍将在它们自己的设置下被控制

**描述你考虑过的替代方案**我考虑了一个与上面类似的想法，用户在休假期间必须完全注销。

**附加背景**现在，无论出于什么原因，当用户不得不删除他们在网站上的存在时，他们无法恢复。他们必须创建一个新账户。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3252)

*   @ksato1995 报告了一个错误，在你的个人资料中有太多的信息会溢出并被删除。谢谢，@ksato1995！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 简介溢满期 #3253](https://github.com/thepracticaldev/dev.to/issues/3253) 

[![K-Sato1995 avatar](img/3e88f15f716926d79bcac1c362287830.png)](https://github.com/K-Sato1995) **[K-Sato1995](https://github.com/K-Sato1995)** posted on [<time datetime="2019-06-21T00:34:15Z">Jun 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3253)

**描述 bug**

当用户在他/她的个人资料中放入太多信息时，这些信息会被掩盖。应该是可以滚动什么的。

有点难以用语言解释，但我认为从截图中很容易理解我想说的。

**重现**

1.  检查她的个人资料

[https://dev.to/mollynem](https://dev.to/mollynem)

所有的信息都应该是可读的。

**截图**

[![image](img/64df1069f6286914be51b793f8c0be44.png)](https://user-images.githubusercontent.com/32632542/59889471-81826880-9407-11e9-9634-2f1e70c707a6.png)

**桌面(请填写以下信息):**

*   操作系统:
*   浏览器:
*   版本:

**智能手机(请填写以下信息):**

*   设备:
*   操作系统:
*   浏览器:
*   版本:

**附加上下文**

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3253)

*   [@gypsydave5](https://dev.to/gypsydave5) 为 RSS 阅读器申请了一个功能，可以跟踪链接以获取帖子的内容。谢谢， [@gypsydave5](https://dev.to/gypsydave5) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  RSS 阅读器跟随链接获取文章 #3254](https://github.com/thepracticaldev/dev.to/issues/3254) 

[![gypsydave5 avatar](img/5ea2bf1f343025e1a370cd18e4009de7.png)](https://github.com/gypsydave5) **[gypsydave5](https://github.com/gypsydave5)** posted on [<time datetime="2019-06-21T08:38:24Z">Jun 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3254)

**您的功能请求是否与某个问题相关？请描述一下。**我的博客的 RSS 提要(【https://blog.gypsydave5.com/feeds/feed.rss】T2)不会将博客内容发布到提要中的每个条目。我的印象是，这是通过 RSS 发布到 dev.to 所需要的。

**描述您想要的解决方案**我希望 dev.to RSS 阅读器向每个`<item>`中的`<link>`发出 HTTP 请求，以获取文章，解析它，然后提取围绕`<article>`标签(看起来像是合适的标签)的 HTML。如果这些都失败了，读者应该回到以前的行为。

**描述你考虑过的替代方案**我*可以*开始将内容放入`<content>`标签中，但我觉得这是错误的，因为

*   它不在 RSS 规范中
*   RSS 项目中已经有内容的链接
*   这将不必要地增加提要的大小。

我很高兴在这上面工作，但是我的 Ruby 生锈了。

(然而，我的铁锈不是红宝石色的...<g-emoji class="g-emoji" alias="stuck_out_tongue_winking_eye" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f61c.png">😜</g-emoji>)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3254)

*   [@tducasse](https://dev.to/tducasse) 报告了一个问题，页面之间的阅读时间估计不一致，即帖子本身和包括该帖子在内的搜索结果。谢谢， [@tducasse](https://dev.to/tducasse) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 阅读时间估计不一致跨页 #3257](https://github.com/thepracticaldev/dev.to/issues/3257) 

[![tducasse avatar](img/8c67902b17860e61b513152f859dc636.png)](https://github.com/tducasse) **[tducasse](https://github.com/tducasse)** posted on [<time datetime="2019-06-21T11:42:01Z">Jun 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3257)

**描述 bug**

搜索结果和文章中的时间估计是不一样的。

我认为有些人是对的？我会说搜索结果里的那个好像小了点。

**要重现**要么:

*   点击[这里](https://dev.to/archiewald/create-react-app-with-typescript-tutorial-291d)
*   这里的[和](https://dev.to/search?q=create%20react%20app%20with%20typescript%20tutorial)

或者:

*   转到搜索栏
*   打些东西
*   选择一篇文章，阅读时间估计
*   点选文章，查看预估

**预期行为**

这两个估计应该是相同的。

**截图**

[![Screen Shot 2019-06-21 at 9 22 11 pm](img/11ff5ca1d10c40e7ee024bedc3dbffc8.png)](https://user-images.githubusercontent.com/11507599/59920233-356a0f00-946d-11e9-8461-7388c7b9e7bd.png)

[![Screen Shot 2019-06-21 at 9 22 21 pm](img/a8a4ce5bc0929796c5131a20aca8b3ad.png)](https://user-images.githubusercontent.com/11507599/59920234-356a0f00-946d-11e9-87f1-28b239cf1bf0.png)

**桌面(请填写以下信息):**

*   操作系统:Mac OS Mojave 10.14.5
*   浏览器:铬
*   版本:75.0.3770.100

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3257)

*   [@stargator](https://dev.to/stargator) 报告了通知页面不显示所有通知的问题。对于任何想知道的人，我们目前不分页通知。更多细节请见本期。谢谢，[@观星者](https://dev.to/stargator)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 通知页面需要分页 #3259](https://github.com/thepracticaldev/dev.to/issues/3259) 

[![Stargator avatar](img/4423c630f92bcc7f6a28f43d6d12dca0.png)](https://github.com/Stargator) **[Stargator](https://github.com/Stargator)** posted on [<time datetime="2019-06-21T15:28:08Z">Jun 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3259)

**描述 bug** 我看到我有 138 个通知，我去了页面，但是页面上只列出了 53 个项目，并且没有提供查看其他未看到的通知的方法。

此外，当我进入通知页面时，我**没有看到的通知已经被标记为已看到。**

**问题是**:

*   并非所有通知都会显示。
*   未显示的通知被标记为已查看。

**重现**

1.  在收到超过 53 个通知之前，不要进入您的通知页面。所以 55-60 应该足够了。
2.  单击主页上的通知图标
3.  计算页面上的通知数量，最多应该达到 53 项。
4.  回到主页，注意它不再显示 55-60 个通知，尽管您只看到了 53 个。

**预期行为**我希望通知在出现在浏览器的活动视图之前不会被标记为可见。因此，当通知页面呈现时，只有在第一个视图中看到的初始通知应该被标记为可见。

此外，我希望能够向下滚动或翻页查看我的所有通知。

**截图** [![Screen Shot 2019-06-21 at 11 16 33 AM](img/49ccb6cb58882ced10e77847e496c3bf.png)](https://user-images.githubusercontent.com/7527155/59932951-610dda80-9416-11e9-8d32-3ff6e87441e6.png)

**桌面(请填写以下信息):**

*   操作系统:Mac 10.14.5
*   浏览器:铬
*   版本:75.0.3770.100

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3259)

*   @markel 报告了一个小故障预览无法正常工作的问题。谢谢，[@马克尔](https://dev.to/markel)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 小故障预演出现意外错误 #3261](https://github.com/thepracticaldev/dev.to/issues/3261) 

[![MarkelFe avatar](img/c65a993fdec4650e462aa7d769eea202.png)](https://github.com/MarkelFe) **[MarkelFe](https://github.com/MarkelFe)** posted on [<time datetime="2019-06-21T16:44:57Z">Jun 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3261)

**描述 bug** Glitch 返回错误

> 未知:服务器错误。发生了意外错误。

当加载各种故障 iframes 时(基于我所能做的一点测试)

**重现**

1.  转到[这篇文章](https://dev.to/glitch/on-glitch-this-week-4fo6)或任何包含多个故障 iframes 的文章

2.  完成后，应该会出现错误

**预期行为**当 iframe 正确加载时，如果您按下*“重试”*，第一次应该会正确加载。

**截图**

[![Screenshot_20190621-093851_Chrome~2](img/e8fa8417427a621ba936843559588baf.png)](https://user-images.githubusercontent.com/45839898/59937921-809e0680-9408-11e9-8fc1-8299f9ca7d31.png)

**桌面(请填写以下信息):**

*   操作系统:
*   浏览器:
*   版本:

**智能手机(请填写以下信息):**

*   设备:PocoPhone F1
*   操作系统:安卓 9(口味:LineageOS)
*   浏览器:谷歌浏览器 PWA
*   版本:75

**附加上下文**

*   我还不能测试如果你加载一个小故障 iframe 会发生什么。
*   我还没有调查过 Glitch 提供的响应代码，因为我在移动。知道它会有帮助。
*   这很可能是一个系统，以避免机器人超载故障网站。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3261)

# 开发-iOS

我们本周在 iOS 回购方面没有太多活动。随意[提问题](https://github.com/thepracticaldev/dev-ios/issues/new)，[看代码库](https://github.com/thepracticaldev/dev-ios)，或者[提拉请求](https://github.com/thepracticaldev/dev-ios/pulls)！

# DEV-Android

## 特性

*   [@sierisimo](https://dev.to/sierisimo) 把代码移到 JDK8。更多详情请见 PR。谢谢， [@sierisimo](https://dev.to/sierisimo) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将代码移入 JDK8  #40](https://github.com/thepracticaldev/DEV-Android/pull/40) 

[![sierisimo avatar](img/6eb86ef21a627d5d4e6629501f8f9e9c.png)](https://github.com/sierisimo) **[sierisimo](https://github.com/sierisimo)** posted on [<time datetime="2019-06-19T05:14:21Z">Jun 19, 2019</time>](https://github.com/thepracticaldev/DEV-Android/pull/40)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

我正在努力为这个项目做贡献，我要做的第一件事是更新项目中的库，我们可以为 kotlin 和编译规则迁移到 JDK8。这主要是一个建议，因为如果不用 JDK8 编译，一些库会抛出错误。

### 也…

*   移动了为 kotlin 应用插件的顺序(根据配置会抛出一个错误或警告)。警告/错误声明`kotlin-android`应该在“kotlin-android-extensions”之前
*   更新了库的版本:
    *   androidx . test:runner->**1 . 1 . 1->1 . 2 . 0**
    *   androidx . test . espresso:espresso-core->**3 . 1 . 1->3 . 2 . 0**

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![Hey](img/a26682f720699d94cc8750205bd029f7.png)](https://camo.githubusercontent.com/37cb4eeaa261b192c88c0a5ac765b9efeaeba510/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f785431545458715a6666554c634a776c4c572f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/pull/40)

这星期到此为止！敬请关注下周的版本。