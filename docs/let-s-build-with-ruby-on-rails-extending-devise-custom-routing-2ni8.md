# 让我们用 Ruby on Rails 构建——扩展设备——定制路由

> 原文：<https://dev.to/justalever/let-s-build-with-ruby-on-rails-extending-devise-custom-routing-2ni8>

继续我的“让我们用 Ruby on Rails 构建——扩展 device 系列”,我将看看如何利用 Devise gem 在 Ruby on Rails 应用程序中设计定制路由选项。

[查看源代码](https://github.com/justalever/extending-devise-custom-routing)

下载我在本教程中引用的启动顺风模板(可选):

【https://github.com/justalever/kickoff_tailwind T2】

[https://www.youtube.com/embed/5T0LK02wRSE](https://www.youtube.com/embed/5T0LK02wRSE)

在本指南中，你将学习如何使用我的`kickoff_tailwind`模板(链接如上)来安装和创建一个新的 Ruby on Rails 应用程序。在许多其他配置中，该模板设置了立即快速使用的设备。devise 自带了一些默认设置，但我为每个给定的用户帐户添加了几个新字段(用户名、姓名)。

在 devise gem 的上下文中，定制路由非常容易。一个全新的 ruby on rails 应用程序的基本安装可能如下所示:

```
Rails.application.routes.draw do
  devise_for :users # default custom method for rendering all routing
  root to: "home#index" # devise requires some sort of root option to redirect back to
end 
```

Enter fullscreen mode Exit fullscreen mode

扩展默认路径，比如从`/users/sign_in`或`/users/sign_up`开始，可能是你想要基于你自己的应用逻辑和/或品牌定制的东西。这样做很简单。最后，我做了以下更改。

```
Rails.application.routes.draw do

  devise_for :users,
    path: '', # optional namespace or empty string for no space
    path_names: {
      sign_in: 'login',
      sign_out: 'logout',
      password: 'secret',
      confirmation: 'verification',
      registration: 'register',
      sign_up: 'signup'
    }

  root to: 'home#index'
end 
```

Enter fullscreen mode Exit fullscreen mode

上面你可以看到默认值是如何被字符串值覆盖的，这实际上只是相应地更新了 URL 路径名。你不需要在你的应用程序中修改视图或控制器名称，这很好，但也有点令人困惑，因为大多数时候这些东西是匹配的。我和 Devise 一起工作了足够长的时间，这个问题不会对我产生太大的影响，但它可能是值得你自己的项目考虑的事情。

### 到目前为止的级数

*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——添加定制字段](https://web-crunch.com/extending-devise-custom-fields)
*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——确认邮件](https://dev.to/justalever/let-s-build-with-ruby-on-rails-extending-devise-series-confirmation-emails-egf)

### 无耻的塞时间

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[让我们用 Ruby on Rails 构建——扩展设备——定制路由](https://web-crunch.com/lets-build-with-ruby-on-rails-extending-devise-custom-routing/)最早出现在[网站](https://web-crunch.com)上。