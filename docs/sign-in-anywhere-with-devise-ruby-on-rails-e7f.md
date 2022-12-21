# 使用 device-Ruby on Rails 在任何地方登录

> 原文：<https://dev.to/justalever/sign-in-anywhere-with-devise-ruby-on-rails-e7f>

[design gem](https://github.com/plataformatec/devise)附带一个默认配置，允许您在特定路线上登录。这篇文章提出了改变指导方针的想法，允许用户在任何地方登录。

[https://www.youtube.com/embed/aJ6XXxU1Zgs](https://www.youtube.com/embed/aJ6XXxU1Zgs)

### 初始设置

出于演示的目的，我使用了自己制作的名为 [kickoff_tailwind](https://github.com/justalever/kickoff_tailwind) 的 Ruby on Rails 应用程序模板。它利用了一些附加功能，最终节省了我创建和配置 Rails 应用程序的时间。配置 Devise 是我的应用程序模板为我节省的时间之一。我还扩展了`User`模型，以包括`name`、`username`和`admin`属性。这些都不是默认的。

所有这些都是为了告诉你，欢迎你使用我的模板或从头开始滚动一个新的 Rails 应用程序。在视频中，你会看到我具体引用我的模板。

### 扩展定计逻辑

允许登录表单出现在给定的 Ruby on Rails 应用程序中的其他地方的最简单的方法是在呈现表单时包含一些 Devise depends 的方法。如果你安装了我安装的 devise views，你可以看到在`app/views/devise/sessions`里面有一个专门用于呈现登录表单的模板。

我把这个复制粘贴到`app/views/shared/_login.html.erb`里。这是我创建的一个新文件夹和文件，可以很容易地在我们的应用程序中的任何地方呈现。为了提高可重用性，我把它放在一个新的`shared`文件夹中。

在`localhost:3000`上渲染模板现在会导致错误。Devise 需要一些不可用的变量。我们可以通过多种方式添加这些方法/变量，但最简单的是将其作为助手逻辑添加到`app/helpers/application_helper.rb`中。这样，我们几乎可以在应用程序的任何地方访问这些内容。

```
# app/helpers/application_helper.rb

module ApplicationHelper
  def resource_name
    :user
  end

  def resource
    @resource ||= User.new
  end

  def resource_class
    User
  end

  def devise_mapping
    @devise_mapping ||= Devise.mappings[:user]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用上面定义的方法，您应该能够重新加载您的根路径并看到登录表单。我将更详细地介绍正在发生的事情，以及如何在视频中有条件地呈现登录表单。这个概念使得登录变得轻而易举，并且最终不会强迫您使用 Devise gem 默认值。很酷的东西！

### 到目前为止的级数

*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——添加定制字段](https://web-crunch.com/extending-devise-custom-fields)
*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——确认邮件](https://dev.to/justalever/let-s-build-with-ruby-on-rails-extending-devise-series-confirmation-emails-egf)
*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——定制路由](https://dev.tolets-build-with-ruby-on-rails-extending-devise-custom-routing)
*   [让我们构建:使用 Ruby on Rails——扩展 device 系列——扩展 device——使用用户名或电子邮件登录](https://web-crunch.com/devise-login-with-username-email)

### 无耻的塞时间

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[今天就下载你的拷贝吧！](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[使用 device-Ruby on Rails](https://web-crunch.com/sign-in-anywhere-devise-ruby-on-rails/)在任何地方登录首先出现在[的 Web-Crunch](https://web-crunch.com) 上。