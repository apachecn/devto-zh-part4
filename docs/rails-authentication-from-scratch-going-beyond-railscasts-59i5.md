# 从头开始的 Rails 认证。超越铁路成本

> 原文：<https://dev.to/npras/rails-authentication-from-scratch-going-beyond-railscasts-59i5>

如果您必须在 rails 应用程序中实现身份验证，Devise 是您最安全的选择。滚自己的就是搬起石头砸自己的脚。

但是使用 Devise 对我来说不像是编码。这就像通过阅读网上的指导性博客文章来设置你的新 mac。Devise 有很棒的文档，涵盖了你所有的问题。你只要遵循它们，就能获得行业级的安全性。

但是，如果我们能够理解设计和认证一般是如何工作的，这将是很好的编码实践。

所以我按照著名的 [Ryan Bates 教程](http://railscasts.com/episodes/250-authentication-from-scratch-revised)从头开始实现它。但是真正的动机来自 Justin Searls，他在最近的演讲[【自私的程序员】](http://blog.testdouble.com/posts/2019-05-08-the-selfish-programmer)中说他自己并不理解 Devise，所以从零开始为他的一个副业项目实现了认证。他自己完成了通常的工作流程——注册、登录、忘记密码、重置密码等——这有助于他“将应用程序的所有代码都记在脑子里”。(在你参与的项目的整个生命周期中，你也应该处于这种状态。)

我做了同样的事情。但是在主要的工作流程之后，我开始实现其他功能，类似于 Devise 以前实现它们的方式。我只是克隆了他们的 repo 并在其中搜索了一个特性是如何实现的。对于设计支持的每个特征，他们考虑所有可能的边缘情况。我不在乎。所以我只选取了这个特性的核心部分，并对其进行了编码。

我实现的功能有:

*   用户注册
*   通过电子邮件和密码进行身份验证(登录和注销)
*   记住并重定向到一个需要授权的页面，该页面是用户在注销然后登录时试图访问的
*   用户确认(只有经过确认的用户才能执行某些/所有操作)
*   忘记密码和密码重置

在本节的其余部分，我将解释我是如何实现这些的。对于这里缺失的代码片段，你可以在实际的回购协议中找到:[https://github.com/npras/meaningless](https://github.com/npras/meaningless)。

* * *

### 用户数据库设计

这是显示所有字段、索引和数据类型的用户迁移文件。

```
class CreateUsers < ActiveRecord::Migration[6.0]
  def change
    create_table :users do |t|
      t.string :name

      t.string :email, null: false
      t.string :password_digest

      t.string :remember_token

      t.string :password_reset_token
      t.datetime :password_reset_sent_at

      t.string :confirmation_token
      t.string :unconfirmed_email
      t.datetime :confirmation_sent_at
      t.datetime :confirmed_at

      t.timestamps
    end

    add_index :users, :email, unique: true
    add_index :users, :remember_token, unique: true

    # I like to use empty lines to group related code
    add_index :users, :password_reset_token, unique: true

    add_index :users, :confirmation_token, unique: true
    add_index :users, :unconfirmed_email, unique: true
  end
end 
```

### 代码结构

devise 的所有控制器都继承自`DeviseController`。我希望我的认证功能继承自`PrasDeviseController`。

(不仅仅是因为虚荣的原因。早些时候，我让用户创建(注册)发生在 UsersController 中，这使得很难在一个 ancenstral 控制器中提取所有公共代码。那时我意识到 Devise 有一个特殊的东西叫做注册，这是用户创建的地方。这允许我们让 UsersController 做非认证的事情，同时将用户创建代码提取到与认证相关的代码中。*好吧，命名还是虚空。*)

`PrasDevise`控制器托管其他 auth 相关代码使用的所有公共方法。例如，如果您打算在任何 auth 表单中使用 recaptcha 代码，这是放置它们的好地方。我把它们放在每一个地方——注册、登录、重设密码等等，只是为了惹恼用户(仅仅是我)。下面是 recaptcha 使用的 2 个方法:[https://github . com/npras/needless/blob/master/app/controllers/pras _ device _ controller . Rb # L70-L79](https://github.com/npras/meaningless/blob/master/app/controllers/pras_devise/pras_devise_controller.rb#L70-L79)。

因为控制器是有作用域的，如果 URL 也有作用域就好了。下面是`routes.rb`文件的样子:

```
 scope module: :pras_devise do
    resources :registrations, only: [:new, :create]
    resources :confirmations, only: [:new, :show]
    resources :sessions, only: [:new, :create, :destroy]
    resources :password_resets, only: [:new, :edit, :create, :update]
  end 
```

这些控制器文件都在`app/controllers/pras_devise/`文件夹中。

### 用户注册工作流程

我希望允许所有经过验证的操作只由经过确认的用户执行。确认用户是指点击了发送到他们电子邮件中的特殊链接的用户，他们通过注册声称该链接是他们的。

在`create`动作中，我们仅通过`unconfirmed_email`字段而不是`email`字段来查找/初始化用户。一旦用户确认，我们将删除未确认领域的电子邮件。

```
 # in pras_devise/registrations_controller.rb
    def create
      @user = User.find_or_initialize_by(unconfirmed_email: user_params[:email])
      @user.attributes = user_params
      if @user.save
        @user.generate_token_and_send_instructions!(token_type: :confirmation)
        redirect_to root_url, notice: "Check your email with subject 'Confirmation instructions'"
      else
        render :new
      end
    end

    private def user_params
      params
        .require(:user)
        .permit(:name, :email, :password, :password_confirmation)
    end 
```

(请注意创建操作中的第二行。我发现给 activerecord 对象的所有属性分配一个类似散列的数据结构是一个很好的方法。)

### 用户确认工作流程

`user.generate_token_and_send_instructions!`方法只是生成一个惟一的 confirmation_token，并向该用户发送一封电子邮件，其中包含一个包含该 token 的链接。

```
 # in models/user.rb

  # token_type is:
  # confirmation for confirmation_token,
  # password_reset for password_reset_token
  # etc.
  def generate_token_and_send_instructions!(token_type:)
    generate_token(:"#{token_type}_token")
    self[:"#{token_type}_sent_at"] = Time.now.utc
    save!
    UserMailer.with(user: self).send(:"email_#{token_type}").deliver_later
  end 
```

邮件程序方法看起来是这样的:

```
 # in mailers/user_mailer.rb
  def email_confirmation
    @user = params[:user]
    @email = @user.unconfirmed_email
    @token = @user.confirmation_token

    mail to: @email, subject: "Confirmation instructions"
  end 
```

而邮件本身看起来是这样的:

```
<p>Welcome <%= @email %>!</p>

<p>You can confirm your account email through the link below:</p>

<p><%= link_to 'Confirm my account', confirmation_url(@user, confirmation_token: @token) %></p> 
```

链接看起来像这样:`http://example.com/confirmations/111?confirmation_token=sOmErandomToken123`其中`111`是用户的 id，这在这里是不相关的。我们只需要它，因为我们正在以 restful 方式定义相关的控制器动作。

在`confirmations_ocntroller#show`动作中，我们通过`confirmation_token`参数查找用户。如果令牌还没有过期，那么我们通过将`unconfirmed_email`标记为 0 并保存记录来确认它们。

```
# confirmation_controller.rb
    def show
      user = User.find_by(confirmation_token: params[:confirmation_token])

      if user.confirmation_token_expired?
        redirect_to new_registration_path, alert: "Confirmation token has expired. Signup again." and return
      end

      if user
        user.email, user.unconfirmed_email = user.unconfirmed_email, nil
        user.confirmed_at = Time.now.utc
        user.save
        redirect_to root_url, notice: "You are confirmed! You can now login."
      else
        redirect_to root_url, alert: "No user found for this token"
      end
    end 
```

请注意，如果令牌过期，我们将重定向到注册页面。如果用户现在试图用同一个电子邮件注册，它仍然可以工作，因为在`registrations_controller#create`中，每当有人试图注册时，我们使用`User.find_or_initialize_by`而不是`User.new`。

### 签到/签退工作流程

这很简单。

*   根据从登录表单传入的电子邮件，从数据库中找到用户
*   尝试使用传入的密码来验证用户
*   如果认证成功，创建一个唯一的`remember_token`，加密并保存在 cookie 中。

```
 def create
      if @user&.authenticate(params[:password])
        login!
        redirect_to after_sign_in_path_for(:user), notice: "Logged in!"
      else
        flash.now.alert = "Email or password is invalid"
        render :new
      end
    end

    private def login!
      unless @user.remember_token
        @user.generate_token(:remember_token)
        @user.save
      end
      if params[:remember_me]
        cookies.encrypted.permanent[:remember_token] = @user.remember_token
      else
        cookies.encrypted[:remember_token] = @user.remember_token
      end
    end 
```

如果用户在登录表单中勾选了`Remember me`复选框，那么我们创建一个永久的 cookie，否则它只是一个普通的 cookie。

如果身份验证成功，我们会将用户重定向到他们之前尝试访问但没有成功的页面，因为他们当时没有通过身份验证。这部分代码直接取自 Devise。都在母控制器`PrasDeviseController`里。

让我们详细看看它是如何实现的...

### 登录后重定向到特定页面

为此，首先需要保存用户在会话中访问的每个页面(一个奇特的 cookie)。但是不是所有页面都应该被保存。Devise 仅保存满足所有这些条件的请求:

*   该请求应该是 GET 请求。其他任何事情听起来都很危险，实现起来也不简单。(这里有一个关于这个的有趣的[栈溢出讨论](https://softwareengineering.stackexchange.com/questions/99894/why-doesnt-http-have-post-redirect)。)
*   该请求不应是 ajax 请求
*   请求应该是针对不是来自任何 PrasDevise 控制器的操作。也就是说，它不应该用于登录、注册、忘记密码等页面。没道理
*   请求格式只能是 html

然后，这些类型的请求被存储在一个带有关键字`:user_return_to`的会话 cookie 中。一旦用户成功登录，那么`sessions_controller#create`动作会将他们重定向到正确的

所以，这里有一个 before_action 回调函数，每次请求应用程序时都会调用它。

```
# in pras_devise_controller.rb

    before_action :store_user_location!, if: :storable_location?

    private def storable_location?
      request.get? &&
        is_navigational_format? &&
        !is_a?(PrasDevise::PrasDeviseController) &&
        !request.xhr?
    end

    private def is_navigational_format?
      ["*/*", :html].include?(request_format)
    end

    private def request_format
      @request_format ||= request.format.try(:ref)
    end

    private def store_user_location!
      # :user is the scope we are authenticating
      #store_location_for(:user, request.fullpath)
      path = extract_path_from_location(request.fullpath)
      session[:user_return_to] = path if path
    end

    private def parse_uri(location)
      location && URI.parse(location)
    rescue URI::InvalidURIError
      nil
    end

    private def extract_path_from_location(location)
      uri = parse_uri(location)
      if uri 
        path = remove_domain_from_uri(uri)
        path = add_fragment_back_to_path(uri, path)
        path
      end
    end

    private def remove_domain_from_uri(uri)
      [uri.path.sub(/\A\/+/, '/'), uri.query].compact.join('?')
    end

    private def add_fragment_back_to_path(uri, path)
      [path, uri.fragment].compact.join('#')
    end

    private def after_sign_in_path_for(resource_or_scope)
      if is_navigational_format?
        session.delete(:user_return_to) || root_url
      else
        session[:user_return_to] || root_url
      end
    end 
```

(这一切都来自于 design。)

### 密码重置工作流程

对于密码重置，您需要 4 个动作。

*   `new`显示用户输入电子邮件的表单。
*   然后，它将被提交到`create`，应用程序将生成一个`password_reset_token`，并通过电子邮件发送到接收邮件。
*   然后，用户将点击电子邮件中的链接，这将把他带到一个`password edit`页面，在那里用户通过链接中的`password_reset_token`被找到。
*   一旦用户用新密码填写表单并提交，它将转到`update`动作，将新密码保存在数据库中。

这里是控制器代码:

```
# password_resets_controller.rb

    def new
    end

    def create
      user = User.find_by(email: params[:email])
      user&.generate_token_and_send_instructions!(token_type: :password_reset)
      redirect_to root_url, notice: "If you had registered, you'd receive password reset email shortly"
    end

    def edit
      set_user
      redirect_to root_url, alert: "Cannot find user!" unless @user
    end

    def update
      set_user
      if (Time.now.utc - @user.password_reset_sent_at) > 2.hours
        redirect_to new_password_reset_path, alert: "Password reset has expired!"
      elsif @user.update(password_update_params)
        redirect_to root_url, notice: "Password has been reset!"
      else
        render :edit
      end
    end

    private def set_user
      @user = User.find_by(password_reset_token: params[:id])
    end

    private def password_update_params
      params
        .require(:user)
        .permit(:password, :password_confirmation)
    end 
```

请注意，提交更新表单时，我们确保 password_reset 电子邮件是最近发送的。我们不希望用户滥用这个功能。

`email_password_reset.html.erb`模板看起来像这样:

```
To reset your password, click the URL below.

<%= link_to edit_password_reset_url(@user.password_reset_token), edit_password_reset_url(@user.password_reset_token) %>

If you did not request your password to be reset, just ignore this email and your password will continue to stay the same. 
```

### 结论

花时间在任何图书馆的引擎盖下寻找是很棒的。在 design repo 中的示例代码和测试案例的帮助下，我能够将碎片放在一起，并找出一些主要功能是如何工作的。我也碰到过很多 succint 和漂亮代码的样本，尤其是他们的测试用例。仅仅通过使用 minitest 和 mocha，他们已经编写了简短但易读的测试用例。

如果你花时间带着好奇心去探索，没有什么是神秘的。