# 辛纳特拉项目:新鲜的味道

> 原文：<https://dev.to/michaellalatkovic/sinatra-project-fresh-bites-3hb6>

对于 Learn.co 软件工程课程的 Sinatra 部分的最终评估，任务是使用 Sinatra 创建一个 CRUD、MVC 应用程序。

我创建了一个简单的演示 web 应用程序，名为 **Fresh Bites** 来练习使用我到目前为止学到的所有技能和工具。你可能会问，什么是新鲜的食物？

Fresh Bites 允许用户创建一个帐户来管理他们毛茸茸的好朋友的食谱。基本上，我只是想找个借口用狗粮照片。

[![](img/44c5276128bd0642b7baa76795aeafd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ems4RPH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jos0ic48p87fzby5nxp1.png)

如果你想快速创建一个简单的 web 应用程序，下面是我从概念到功能应用的步骤。

### 第一步:建立关系模型

需要的一些基准规范如下:

*   包括不止一个模型类
*   在您的用户模型中至少包含一个 has_many 关系
*   在另一个模型上至少包括一个“隶属于”关系

我发现先描绘出我的关系很有帮助——甚至在打开我的终端开始构建任何东西之前。

我知道我想要一个用户模型和食谱模型。我决定添加一个类别模型，这将是一个很好的补充，这意味着我需要添加第四个也是最后一个 Recipe_category 模型，如下所示:

[![](img/d0129e25d26f50743122bbf1d9e7d809.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xyzosZEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e7pus6far2dudai4zl6v.png)

### 第二步:角膜模板

我用[角膜](https://github.com/thebrianemory/corneal)红宝石来布局我的基本 app 结构。这不仅节省了时间，而且非常全面！我绝对推荐尝试一下！

### 第三步:数据库迁移

在 ActiveRecord 的帮助下，我为我的每个模型创建了数据库迁移…我确实意识到我忘记了在我的 recipes 表中添加一个 user_id 列，该列添加了最后一次迁移。幸运的是，使用一些 gem(active record、sinatra-activerecord 和 rake)，做到这一切非常容易。要了解更多关于这些 gem 如何设置 ActiveRecord 以将数据库添加到 Sinatra 应用程序的信息，请查看[这篇文章](https://learn.co/lessons/sinatra-activerecord-setup)，我发现它非常有用。

[![](img/581f8c8e6e2e4d387d886ba6ca49f14a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uZpZaGVT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhjvijkxb3dv97ccrdih.png)

###### 最后一组数据库迁移的快照

### 第四步:模型—视图—控制器(MVC)

有了已经用角膜宝石创建的基本模板，我更深入地研究了 MVC 架构模式:

[![](img/65952aed513c0df47f74b59366ec2261.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ihIbRv4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i0frw1vqishpz7akx1r8.png)

###### 最终 MVC 文件结构的快照

#### 创建我的模型

已经画出了我的模型关系，这是一个非常简单的任务。我创建了四个模型(用户、配方、类别和配方 _ 类别),它们都继承自活动记录。如果你想了解更多关于如何做到这一点的细节——坦率地说，还有接下来的一切——我强烈推荐[这个指南](https://guides.rubyonrails.org/active_record_basics.html)。

太棒了，我建议不要再看这个了，直接去那里吧😊

#### 创建我的视图

用户—注册、登录、索引和显示视图页面。
配方——索引、显示、新建和编辑视图页面。
类别—只需要一个索引和显示视图。

此外，我创建了一个欢迎页面和一个注销视图页面。

仅供参考:layout.erb 文件保存了我所有的样式，以保持我所有页面的一致性。我在 layout.erb 文件中使用了 [yield 语句](https://learn.co/lessons/sinatra-yield-readme),在这里我希望加载其他视图页面内容。

#### 创建我的控制器

这是我们构建 CRUD(创建、读取、更新、删除)功能的地方。

我总结了我在应用程序中包含的控制器动作。我用这篇文章作为我如何实现 RESTful 路线的指南。如果你对代码感兴趣，我在这篇博客的末尾添加了一个 GitHub repo 的链接！

*应用 _ 控制器*

*   转到我的欢迎页面&一些辅助方法

*用户 _ 控制器*

*   索引操作(显示所有用户)和显示操作(显示单个用户)
*   获取显示注册页面的请求并发布创建新用户帐户的请求
*   获取显示登录页面的请求，并发布登录到现有帐户的请求
*   获取注销请求

*配方 _ 控制器*

*   索引操作(显示所有配方)和显示操作(显示单个配方)
*   显示新配方表单的新操作和创建新配方的创建操作
*   编辑操作显示编辑配方表单，更新操作更新配方
*   删除配方的删除操作

*类别 _ 控制器*

*   索引操作(显示所有类别)和显示操作(显示单个类别)

我按照“MVC”的顺序列出了上面的内容，但是，我更喜欢写出模型、控制器、视图…或者更现实的模型，然后在控制器和视图之间交替——当你在控制器中创建 RESTful 路径时，创建相应的视图(erb 文件),然后测试它以确保它能工作。

还有很多东西需要编程(*调试实际上是*)所有的功能和应用程序的前端设计，但这些是我开始时采取的步骤，至少是为了让我的应用程序运行起来！

如果你有兴趣看完整的代码，请随意查看 GitHub repo ！