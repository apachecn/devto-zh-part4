# 如何将 Ruby on Rails 应用程序置于维护模式

> 原文：<https://dev.to/botreetechnologies/how-to-put-a-ruby-on-rails-application-in-maintenance-mode-2k3b>

[![How to put a Ruby on Rails application in Maintenance Mode](img/70bbee0c0c842ec9141da7563917d13c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cF2YvqVy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.botreetechnologies.com/blog/wp-content/uploads/2019/08/ruby-on-rails-application-in-maintenance-mode.png)

## 什么是维护模式？

**维护模式**页面是给网站访问者的用户友好的消息，表明我们正在处理一些网站需要关闭一段时间的事情。我们需要这样做，因为我们不希望我们的网站在网站维护期间的特定时间内中断。

当你雇佣 Ruby on Rails 程序员时，他们可以负责整个维护模式页面。

### 为什么需要维护模式页面？

*   执行升级(将 rails 版本从一个迁移到另一个)
*   在后端执行数据迁移
*   将图像或文件从亚马逊 S3 迁移到谷歌云，反之亦然
*   还有更多...

### 对于 Ruby on Rails 应用程序如何做到这一点？

最简单的方法是使用[道岔](https://github.com/biola/turnout)宝石。Turnout 是 [Rack](http://rack.rubyforge.org/) 中间件，带有 [Ruby on Rails 开发](https://www.botreetechnologies.com/blog/ruby-on-rails-development-obstacles-opportunities)引擎，允许你轻松地将你的应用置于维护模式。

道岔宝石具有以下特点。

*   易于安装
*   打开和关闭维护模式的 Rake 命令
*   无需编辑 maintenance.html 文件，轻松提供每次停机的原因
*   允许某些 IP 或 IP 范围绕过维护页
*   维护期间允许某些路径可用

你可以在这里找到他们所有人。

### 安装

只需将以下宝石添加到您的[宝石文件](https://www.botreetechnologies.com/blog/pagy-a-new-pagination-gem-in-the-realm-of-rails-world)

`gem 'turnout'`

然后做`bundle install`就是这样！

### 激活

`$ rake maintenance: start.`

Tada！结束了。当您这样做时，您将看到以下页面。

[![Alt Maintenance mode](img/4b559116198d087e1c8c297ee000a310.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jE1F2ABp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ilayokadff8n43tosi6t.png)

`$ rake maintenance: start reason= "This is the custom reason!"`

[![Alt Custom reason](img/91cbd115301551c3608117a405363712.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MOvjW53L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6i69vpyzwy9cy4wgebmp.png)

你可以在这里找到所有的命令[。](https://github.com/biola/turnout#activation)

### 停用

`$ rake maintenance: end.`

[提供了默认的维护页面](https://github.com/biola/turnout/blob/master/public/)，但是您可以创建自己的`public/maintenance.[html|json|html.erb]`文件。深入了解纽约市的[专业网络开发人员，了解提高或构建项目质量和整个公司的各种方法。](https://www.botreetechnologies.com/usa/web-development-company-new-york)

Ruby on Rails web 开发公司会确保你在正确的时间激活和停用维护页面。

就是这样！

### 另请阅读:[提升 Ruby on Rails 应用程序性能的三大技巧](http://www.rorexpertsindia.com/blog/top-3-tips-boost-performance-ruby-rails-application/)