# 可再确认:将设备集成到非标准注册系统中

> 原文：<https://dev.to/themagickoala1/reconfirmable-integrating-devise-into-a-non-standard-registration-system-1563>

Devise 使得用 Ruby on Rails 实现完整的用户身份验证系统变得容易，同时只需编写尽可能少的代码。它提供的模块易于集成，并遵循保护您的应用程序的最佳实践。但是有时你的应用程序需要一点创新思维。那么，当你的用户旅程不是 Devise 所期望的时候，你会怎么做呢？

## 你到底在解决什么问题？

考虑一个应用程序，您希望在让用户进入您的应用程序之前确认他们的电子邮件地址。设计可确认模块的标准流程是反过来做。所以你必须手动启动这个功能。但是，当用户更改他们的电子邮件地址时，您也确实希望使用 confirmable 的可重新确认功能，这意味着也要启用 confirmable。现在你有了两个相互竞争的确认系统，你的短裤也乱了！这篇文章将展示如何绕过确认注册，而仍然使用重新确认更改电子邮件。

## 入门

建立一个 Ruby on Rails 项目是一个已经解决的问题，集成 Devise 也是如此，所以我们尽量不要在这上面花太多时间。关于这一点，[device Github repository](https://github.com/plataformatec/devise)有一个很好的教程，这篇文章的其余部分将假设一个没有启用 confirmable 的 device 的工作实现。

## 配置可确认

这一部分将从 Github [上的设计维基中获得大量灵感。假设你的用户模型在`models/user.rb`，你需要做的就是添加`:confirmable` :](https://github.com/plataformatec/devise/wiki/How-To:-Add-:confirmable-to-Users) 

```
devise :registerable, :confirmable 
```

Enter fullscreen mode Exit fullscreen mode

启用 confirmable 将需要向您的 users 表添加四列，因此创建一个迁移脚本:

```
rails g migration add_confirmable_to_devise 
```

Enter fullscreen mode Exit fullscreen mode

在生成的迁移文件`db/migrate/YYYYMMDDxxx_add_confirmable_to_devise.rb`中，添加以下代码:

```
class AddConfirmableToDevise < ActiveRecord::Migration
  # Note: You can't use change, as User.update_all will fail in the down migration
  def up
    add_column :users, :confirmation_token, :string
    add_column :users, :confirmed_at, :datetime
    add_column :users, :confirmation_sent_at, :datetime
    add_column :users, :unconfirmed_email, :string
    add_index :users, :confirmation_token, unique: true

    # User.reset_column_information # Need for some types of updates, but not for update_all.
    # To avoid a short time window between running the migration and updating all existing
    # users as confirmed, do the following
    User.update_all confirmed_at: DateTime.now
    # All existing user accounts should be able to log in after this.
  end

  def down
    remove_columns :users, :confirmation_token, :confirmed_at, :confirmation_sent_at, :unconfirmed_email
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里的第一步是添加必要的列:

```
add_column :users, :confirmation_token, :string
add_column :users, :confirmed_at, :datetime
add_column :users, :confirmation_sent_at, :datetime
add_column :users, :unconfirmed_email, :string 
```

Enter fullscreen mode Exit fullscreen mode

`confirmation_token`栏是您的确认密钥——当用户设置新的电子邮件地址时，需要将该密钥发送给用户，以验证他们是否有权访问收件箱。生成令牌时会填充`confirmation_sent_at`列，成功完成确认后会设置`confirmed_at`列。仅仅略述了这种神奇设计的表面，我只能假设这两个专栏可以进行比较，以防止试图进行多重确认。

添加第四列`unconfirmed_email`，用于可再确认流程。临时电子邮件地址存储在这里，并在确认完成时保存到电子邮件列。听起来很简单，对吗？

我们还没有完全完成——confirmable 为用户模型引入了额外的功能，用户必须确认他们的帐户才能使用该应用程序。但是现有用户呢？他们没有被确认，他们也没有令牌来确认。为了解决这个问题，我们只需运行`User.update_all confirmed_at: DateTime.now`，就这样，我们所有的现有用户都得到确认。

最后，运行`rake db:migrate`，然后通过在`config/initializers/devise.rb` :
中添加/设置以下行，确保在设备配置中启用了再确认

```
config.reconfirmable = false 
```

Enter fullscreen mode Exit fullscreen mode

## 调整再确认

默认情况下，生成的`confirmation_token`使用`SecureRandom.urlsafe_base64(15)`来创建随机令牌。这可以在用户模型中通过覆盖`generate_confirmation_token`来改变。记住，这个方法还设置了`confirmation_sent_at`属性，所以我建议调用 super，然后将`confirmation_token`属性设置为您新的首选值。我们使用它来设置一个 6 位数的密码，以便手动输入到一个连续的客户端流中，尽管您可以使用令牌作为 url 参数来识别用户，从而轻松地使用更标准的带唯一页面链接的电子邮件。

## 绕过可确认注册

这里我们要解决的问题。我们已经在注册过程中确认了用户的电子邮件地址，所以我们不需要在注册后进行确认。但是默认情况下，confirmable 会给用户发送自己的确认邮件。那么我们如何绕过它呢？

幸运的是，我们可以覆盖 Devise 提供的方法，允许您定制注册电子邮件的邮件映射。这是`send_on_create_confirmation_instructions`。默认情况下，它所做的只是调用`send_confirmation_instructions`，允许用户在发送电子邮件之前或之后执行额外的配置。所以第一步是什么都不做:

```
def send_on_create_confirmation_instructions
end 
```

Enter fullscreen mode Exit fullscreen mode

这将阻止发送确认电子邮件，但用户仍然无法登录，因为他们没有得到确认。所以我们稍微扩展一下:

```
def send_on_create_confirmation_instructions
  self.confirm
end 
```

Enter fullscreen mode Exit fullscreen mode

应该可以了。我们知道用户是从我们自己的自定义流中确认的，所以我们可以告诉 Devise，瞧！用户可以正常登录，没有收到任何多余的确认邮件。

## 总结

Devise 为用户模型提供了许多额外的功能，允许我们专注于小的定制，以支持我们的应用程序的单独需求。

这篇文章是一项正在进行的工作，我希望能够在我继续将我们的应用程序与我们优秀的后端团队正在编写的服务器端代码相集成的过程中，根据您的反馈来改进它。如果您能帮助改进本指南，请留下您的评论，无论是建设性的批评还是额外的来源。

## 来源

1.  如何添加:可向用户确认-设计 Github Wiki([https://Github . com/plataformatec/design/Wiki/How-To:-Add-:可向用户确认](https://github.com/plataformatec/devise/wiki/How-To:-Add-:confirmable-to-Users))；
2.  device::Models::confirmatible-device ruby docs([https://www . ruby doc . info/github/plataformatec/device/master/device/Models/confirmatible](https://www.rubydoc.info/github/plataformatec/devise/master/Devise/Models/Confirmable))；
3.  Rails 入门和设计-设计 Github 资源库([https://github.com/plataformatec/devise](https://github.com/plataformatec/devise))；
4.  感谢 verenion 对服务器端代码和应用架构的开发。