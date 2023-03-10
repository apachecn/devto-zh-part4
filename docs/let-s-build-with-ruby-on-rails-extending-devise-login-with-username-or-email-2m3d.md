# 让我们用 Ruby on Rails 构建:扩展设备——用用户名或电子邮件登录

> 原文：<https://dev.to/justalever/let-s-build-with-ruby-on-rails-extending-devise-login-with-username-or-email-2m3d>

欢迎来到我的“用 Ruby on Rails 构建”系列的另一篇文章——在一个系列中扩展 Devise 系列。这篇文章将教你如何允许一个用户用一个用户名或电子邮件使用 Devise gem 登录。

[https://www.youtube.com/embed/qS-Jc2aJH5A](https://www.youtube.com/embed/qS-Jc2aJH5A)

### 入门

在视频截屏中，我引用了我的[kick off _ tailwind](https://github.com/justalever/kickoff_tailwind)Ruby on Rails 应用程序模板。你可以像我一样使用它，或者从头开始。我的模板设置自动设计，所以有更少的前期配置。我还在`User`数据库表中添加了一个新的`username`和`name`列，我们将在本指南中引用它。如果您没有使用模板，请确保至少在您的表上添加一个`username`列。

可能是这样的:

```
$ rails generate migration add_username_to_users username:string 
```

Enter fullscreen mode Exit fullscreen mode

这个命令生成一个新的迁移，负责向数据库表`users`添加一个`username`列。

### 在`User`模型中创建新的`:login`属性

我们需要一种方法来表示新的登录 id 是用户名还是电子邮件。设计 ships，仅使用**`email`作为为每个用户创建新会话(登录)的有效默认值。为了扩展这一点，我们需要调整一下 Devise 的内部工作机制。用户模型变成如下:** 

```
# app/models/user.rb

class User < ApplicationRecord
  attr_accessor :login

  # "getter"
  # def login
  # @login
  # end

  # "setter"
  # def login=(str)
  # @login = str
  # end

  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable

  def self.find_for_database_authentication warden_condition
    conditions = warden_condition.dup
    login = conditions.delete(:login)
    where(conditions).where(
      ["lower(username) = :value OR lower(email) = :value",
      { value: login.strip.downcase}]).first
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个名为`:login`的新`attr_accessor`。这是您在上面那行代码后看到的注释的简写。它本质上为你创建了一个`getter`和一个`setter`，这样你就可以让你的模型更干净。这种情况经常发生，所以这种简写方式对 ruby 来说是一个很好的补充。

在这个类的底部，你会发现一个新的方法`self.find_for_database_authentication`，它通过 warden 扩展了 Devise 来查询。这使用一些 SQL 来查询`username`或`email`字段，假设在表单提交期间提供了一个或另一个。

### 允许新增字段

有了逻辑的大脑，我们可以前往控制器层，允许安全地提交`:login`属性。

```
# app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception

  before_action :configure_permitted_parameters, if: :devise_controller?

  protected

    def configure_permitted_parameters
      devise_parameter_sanitizer.permit(:sign_up,
        keys: [:username, :name, :email, :password, :password_confirmation])
      devise_parameter_sanitizer.permit(:sign_in,
        keys: [:login, :password, :password_confirmation])
      devise_parameter_sanitizer.permit(:account_update,
        keys: [:username, :name, :email, :password_confirmation, :current_password])
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

上面我修改了我的`application_controller.rb`文件，以包含新配置的参数。这实际上是将这些字段列入白名单，这样 Rails 就知道要将它们接收到会话/数据库中。我已经分别给`:sign_in`权限添加了`:login`属性。

### 更新视图层

有了这些核心变化，我们可以扩展我们的视图层来包含它们。

```
<!-- app/views/devise/sessions/new.html.erb-->
<h2 class="heading text-4xl font-bold pt-4 mb-8">Log in</h2>
<%= form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>

  <div class="mb-6">
    <%= f.label :login, class:"label" %>
    <%= f.text_field :login, autofocus: true, class: "input" %>
  </div>

  <!-- additional fields -->
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

如果您还没有将`username`字段添加到您的注册视图中，您也可以这样做。

```
<!-- app/views/devise/registrations/edit.html.erb-->

<h2 class="heading text-4xl font-bold pt-4 mb-8">Edit <%= resource_name.to_s.humanize %></h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>

  <%= render "devise/shared/error_messages", resource: resource %>

  <div class="mb-6">
    <%= f.label :username, class:"label" %>
    <%= f.text_field :username, autofocus: true, class:"input" %>
  </div>

<!-- additional fields -->
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
<!-- app/views/devise/registrations/new.html.erb-->

<h2 class="heading text-4xl font-bold pt-4 mb-8">Edit <%= resource_name.to_s.humanize %></h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>

  <%= render "devise/shared/error_messages", resource: resource %>

  <div class="mb-6">
    <%= f.label :username, class:"label" %>
    <%= f.text_field :username, autofocus: true, class:"input" %>
  </div>

<!-- additional fields -->
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我更新了我的应用程序布局，以适应登录和注销状态，这样更容易注销。

```
<!DOCTYPE html>
<html>
  <head>
    Kickoff
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <meta name="viewport" content="width=device-width, initial-scale=1">

    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= stylesheet_pack_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>

  </head>

 <body class="bg-white">

  <% flash.each do |type, message| %>
    <% if type == "alert" %>
      <div class="bg-red-500">
        <div class="container px-2 mx-auto py-4 text-white text-center font-medium font-sans"><%= message %></div>
      </div>
    <% end %>
    <% if type == "notice" %>
      <div class="bg-green-500">
        <div class="container px-2 mx-auto py-4 text-white text-center font-medium font-sans"><%= message %></div>
      </div>
    <% end %>
  <% end %>

    <header class="mb-4">
      <nav class="flex items-center justify-between flex-wrap bg-gray-100 py-3 lg:px-10 px-3 text-gray-700 border-b border-gray-400">
        <div class="flex items-center flex-no-shrink mr-6">
          <%= link_to "Kickoff", root_path, class:"link text-xl tracking-tight font-semibold" %>
        </div>
        <div class="block lg:hidden">
          <button class="flex items-center px-3 py-2 border rounded text-grey border-gray-500 hover:text-gray-600 hover:border-gray-600">
            Menu<path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z"/>
          </button>
        </div>
        <div class="w-full block lg:flex-1 lg:flex items-center text-center lg:text-left">
          <div class="lg:flex-grow">
            <%= link_to "Basic Link", "#", class: "block mt-4 lg:inline-block lg:mt-0 lg:mr-4 mb-2 lg:mb-0 link" %>
          </div>

          <div class="w-full block lg:flex lg:flex-row lg:flex-1 mt-2 lg:mt-0 text-center lg:text-left lg:justify-end items-center">
            <% if user_signed_in? %>
              <p class="lg:mr-2 px-4">Welcome, <%= current_user.username %></p>
              <%= link_to "Log out", destroy_user_session_path, method: :delete, class:"btn btn-default mb-2 lg:mr-2 lg:mb-0 block" %>
            <% else %>
              <%= link_to "Login", new_user_session_path, class:"btn btn-default mb-2 lg:mr-2 lg:mb-0 block" %>
              <%= link_to "Sign Up", new_user_registration_path, class:"btn btn-default block" %>
            <% end %>
          </div>
        </div>
      </nav>
    </header>

    <main class="lg:px-10 px-4">
      <%= content_for?(:content) ? yield(:content) : yield %>
    </main>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 关闭思绪

希望这被证明是有用的！我相信这将是我在自己的应用程序中前进的一个方向。人们很健忘，有时记住他们的用户名或电子邮件可能是一个挑战。我在这里没有考虑的一个问题是，如果用户忘记了他们的用户名。这通常是你在野外看到的模式。默认情况下，Devise(我不认为)支持这种模式，但它似乎模仿了“忘记密码”的方法。也许我会在以后的文章中深入探讨如何解决这个问题。

如果你有反馈，问题，或者其他什么，请在评论中告诉我。感谢您的关注！

[![Buy Me A Coffee](img/74efe4c56cbe0fbe70b136af9986b04f.png)](https://www.buymeacoffee.com/webcrunch)

### 到目前为止的级数

*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——添加定制字段](https://web-crunch.com/extending-devise-custom-fields)
*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——确认邮件](https://dev.to/justalever/let-s-build-with-ruby-on-rails-extending-devise-series-confirmation-emails-egf)
*   [让我们构建:使用 Ruby on Rails——扩展 Devise 系列——定制路由](https://dev.tolets-build-with-ruby-on-rails-extending-devise-custom-routing)

### 无耻的塞时间

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[拿起你的副本](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[扩展设备——用用户名或电子邮件登录](https://web-crunch.com/devise-login-with-username-email/)首先出现在[网站](https://web-crunch.com)上。**