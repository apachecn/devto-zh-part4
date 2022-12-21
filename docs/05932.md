# 宝石设计初学者指南。

> 原文：<https://dev.to/ackers93/beginner-s-guide-to-the-devise-gem-35hm>

最近，我一直在开发一个追踪锻炼进度的 Rails 应用程序。

正如你在标题中读到的，这篇文章是关于使用 Devise Gem 的，我将通过使用它的最简单形式来创建一个我希望在第一次尝试时就拥有的资源。

Devise Gem 是用于用户认证的用户，它创建注册和登录表单，它还可以用于创建隐私用户帐户。(我确信它可以做更多的事情，但这是我如何使用它。)

因此，与所有其他 gem 一样，您需要以通常的方式安装，将`gem 'Devise'`添加到您的 gem 文件中，然后在您的终端中运行`bundle install`。完成后，你需要运行发电机。`rails generate devise:install`。这个命令安装了一个初始化器，它将打印很多指令，我们现在唯一需要关注的是为设计邮件程序添加一个默认的 URL。为了这个应用，您可以使用建议的方法。

接下来，我们需要创建我们的设计模型，你可以根据你的目的给它起任何名字，(例如用户，管理员，成员或职员)。我将使用“User”，因此我将在我的终端`rails generate devise user`中运行，随后使用`rails db:migrate`。

您应该采取的第一个行动是确保您的 Rails 路线已经为 Devise 进行了配置，这可以通过将以下内容添加到您的`config/routes.rb`文件中来完成

```
devise_for :stages
root to: "stages#index"
# "stages" can be replaced by whatever you name your controller, it's just what I needed to name my controller. 
```

Enter fullscreen mode Exit fullscreen mode

这将为应用程序的身份验证创建所有必要的路径，然后导向主索引页面。

为了让没有帐户的人无法使用您的应用程序，进入他们的控制器，在您的方法开始之前，添加以下助手，

```
before_action :authenticate_user! 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，要确保你的助手符合你的设计模式(用户、管理员等)。)

design 最重要的三个助手如下，

```
user_signed_in?
# I used this to show the user’s email address in the top Navigation bar, and depending on their status to show “Log In” or “Log Out” options.
user_session
# Used to create the Log In occurrence, which remains until I’m logged out, in which case it destroys that session.
current_user
#This is used here to display the User's email in the navigation bar.

<% if user_signed_in? %>
    <%= current_user.email %>
    <ul class="dropdown-menu" role="menu">
              <li><%= link_to 'Profile', edit_user_registration_path %></li>
              <li><%= link_to 'Log out', destroy_user_session_path, method: :delete %></li>
<% else %>
          <li><%= link_to 'Log In', new_user_session_path %></li>
          <li><%= link_to 'Sign Up', new_user_registration_path %></li>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

我的控制器中也使用了`current_user`助手，阻止用户访问和编辑任何其他用户的数据。

```
def new
    @stage = current_user.stages.new
  end

  def create
    @stage = current_user.stages.new(allowed_params)
    if @stage.save
      redirect_to stages_path
    else
      render 'new'
    end
  end

  def edit
    @stage = current_user.stages.find(params[:id])
  end

  def update
    @stage = current_user.stages.find(params[:id])
    if @stage.update_attributes(allowed_params)
      redirect_to stages_path
    else
      render 'edit'
    end
  end

  def destroy
    @stage = current_user.stages.find(params[:id])
    @stage.destroy
    redirect_to stages_path
  end 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我的 Stages 控制器中的每个方法都必须通过 Devise Gem 的`current_user`助手进行认证。

Devise 的另一个有用的方面是运行`$ rails generate devise:views users`将为注册、登录、密码更改和帐户更改创建所有必要的视图。例如，下面是 Devise 为注册页面创建的内容，

```
<h2>Sign up</h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
  <%= render "devise/shared/error_messages", resource: resource %>

  <div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, autocomplete: "email" %>
  </div>

  <div class="field">
    <%= f.label :password %>
    <% if @minimum_password_length %>
    <em>(<%= @minimum_password_length %> characters minimum)</em>
    <% end %><br />
    <%= f.password_field :password, autocomplete: "new-password" %>
  </div>

  <div class="field">
    <%= f.label :password_confirmation %><br />
    <%= f.password_field :password_confirmation, autocomplete: "new-password" %>
  </div>

  <div class="actions">
    <%= f.submit "Sign up" %>
  </div>
<% end %>

<%= render "devise/shared/links" %> 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，已经为你做了很多工作，这意味着你只需要将它融入你正在创造的任何东西的风格中。

我相信这就是我在这篇文章中要讲的全部内容，而不会违背我在博客中关于“最简单形式的设计”的开篇陈述。希望这有助于其他人理解这个难以置信的有用的宝石！

如果你对初学者友好的其他简单使用方法有任何建议，请在下面评论它们！