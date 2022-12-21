# 设计国际化的终极清单

> 原文：<https://dev.to/appsignal/the-ultimate-checklist-to-properly-internationalize-devise-59m6>

有时候，最好的博客文章来自于挠自己痒痒的需要。今天的博文就是一个例子！

您可能知道[device](https://rubygems.org/gems/devise)——一种流行的 Rails 应用程序认证解决方案。在使用 Devise 时，我发现启动和运行翻译比我预想的要复杂得多。过去的我希望有一个清单，可以帮助指导我通过这个过程。

这就是——为了未来的你我——终极设计国际化清单！

## 什么时候(不)使用 design-i18n 宝石？

device 的策略是不使 device 视图中的字符串可翻译。所有字符串都是硬编码的。向 device-i18n gem 的创造者致敬，它使得所有的 device 模板都是可翻译的，并且还提供了来自社区的翻译。对于需要多语言支持的国际应用程序，device-i18n gem 是最简单的解决方案。

然而，device-i18n gem 明确选择尽可能紧密地遵循 device 文本。但是 Devise 提供的文本一开始就不太一致。由于直译永远不会产生流利的语言，我一直在调整翻译(并在项目间复制文件)。这是一个持续的斗争，因为当一次查看一个字符串或一个视图时，很难让所有的翻译保持一致。

结果，i18n-device gem 的翻译质量达不到我的单语、纯荷兰语项目所需的标准。

使用带有定制视图的宝石棱的另一个缺点是。即使只与样式有关，device-i18n gem 的每一次更新，只要涉及到 device 对视图所做的任何更改，就意味着您必须进行修改。您需要重新生成视图并应用定制，或者您必须将更改复制/粘贴到您的视图中。顺便说一下，这个问题不能用下面清单中的方法解决，但是没有 device-i18n gem 也不会更难解决。🤷‍♀

## 🇳🇱各付各的

我决定为 Devise 创建一个漂亮的荷兰语翻译文件，再也不用担心这个问题了。听起来像是一种享受？你可以在 GitHub 上找到[新的和改进的翻译文件](https://github.com/F3PiX/Devise-i18n-checklist/blob/master/devise.nl.yml)。请参见清单中的步骤 3。文件中解释了每个翻译决定的基本原理。

## 终极清单

既然我们已经独立完成了:nl 翻译，让我们从所有的细节开始，并开始检查复选框。

请注意，这里假设您已经准备好了一个应用程序，添加了 device 并安装了一个用户模型，并且生成了 device 视图。如果你正在编码:[这是起点](https://github.com/F3PiX/sandbox-devise-i18n-ed)。请注意，`*.nl.yml`中的`nl`可以替换为您选择的语言，user 和:User 可以替换为您在 Devise 中使用的型号名称。

## 第一步:设置区域设置

将`:nl`添加到可用的区域设置中(如果你对此没有强烈的意见，现在先把它放在`application.rb`中)。现在您可以将其设置为默认值。

```
# application.rb
config.i18n.available_locales = [:nl, :en]
config.i18n.default_locale = :nl 
```

比起将区域设置添加到`application.rb`中，有更好、或许更简洁的选项来设置区域设置。读完这篇文章后，查看 [Rails i18n 指南](https://guides.rubyonrails.org/i18n.html)，选择最适合你的选项。

## 第二步:添加 Rails 翻译

添加 Rails [:nl 翻译文件](https://github.com/svenfuchs/rails-i18n/blob/master/rails/locale/nl.yml)来处理接口中非特定于设备的字符串(尤其是 Rails 验证错误消息)。

## 第三步:定出钥匙

将设计键翻译成`:nl`:增加新的和改进的荷兰语翻译！(🇳🇱上方的链接)

## 第四步:创建视图。*.yml 文件

为视图`views.*.yml`添加荷兰语和英语`.yml`文件。在 repo 中有一个完成的 views.nl.yml 文件的例子，反映了步骤 4、6 和 7。

```
#example views.nl.yml
nl:
  devise:
    # to be filled in later with Devise scopes:
    # registrations:
    #   new:
    #     forget_password: Wachtwoord vergeten? 
```

## 第五步:活动记录属性

现在，为活动记录属性的标签添加翻译，如电子邮件和密码。对于您实现的每个模块，只翻译用户和管理员用户看到的内容。不需要翻译令牌名称等(如 i18n gem 所做的)。以下是我认为有用的“可翻译内容”:

```
# In: active_record.nl.yml
# translating the labels that map to User attributes
nl:
  activerecord:
    attributes:
      user:
        current_password: Huidig wachtwoord
        email: e-mailadres
        password: wachtwoord
        password_confirmation: Wachtwoord bevestigen
        remember_me: Ingelogd blijven?
    models:
      user: Gebruiker 
```

请注意，`:email`和`:password`值是向下减小的。这是因为步骤 7 中对 Rails 错误消息的调整。

现在，将密钥放在`active_record.*.yml`中。

## 第六步:检查每一个设计视图

接下来，所有特定于设备的模板和字符串都需要使用相应的翻译。自己做是一件烦人的工作。在你深入研究之前，看看这些捷径是否对你有用:

*   您可以为视图使用 118n-device gem，但是您仍然需要将键从 gem 的`.yml`文件复制到项目的`.yml`文件中。宝石将它们交错在`devise.*.yml`中，但是我们不想重写我们漂亮的新荷兰语翻译。如果你已经设计了视图的样式，你也需要复制样式。唷。

*   如果您已经有 i18n-ed 项目，您可以复制视图文件。(与前一选项的缺点相同。)

*   使用 repo 获取我在最新项目中使用的一些视图的[示例。](https://github.com/F3PiX/Devise-i18n-checklist/blob/master/devise_views/)

或者一劳永逸地解决它:低头，创建一组文件，你可以从现在开始使用，完全按照你想要的方式。上一步的例子是我自己的第一个版本；他们可能会帮助你启动你自己的。查看下面的小清单，了解所有要求。

### 设计表格的迷你检查表

*   不是活动记录字符串的字符串需要被 118n-ed，并且具有(适当范围的)键和翻译(例如，存储在`views.*.yml`文件中—参见示例)。
*   添加您自己的翻译时，请注意荷兰语翻译与新的`devise.nl.yml`保持一致。
*   忽略降级标签；它们将在步骤 7 中被适当地资本化。
*   如果你已经有了 mailers i18n-ed，没关系。如果没有，您可以忽略它们，直到第 8 步。
*   以下设备视图需要使用 Rails translate ( `t`)助手进行翻译:
    *   `devise/views/shared/_links`部分:所有的链接名；
    *   `devise/views/shared/_errors`偏(自定计 4.6)
    *   在您使用的每个模块的设计表单中:不是活动记录属性标签的所有字符串。

## 第七步:主动记录错误信息

接下来，我们需要调整与 Devise 功能相关的活动记录错误消息。像“密码不能为空”等验证。

事情是这样的，现在我们已经升级了 Devise 翻译，活动记录消息也需要调整，因为风格的不一致性真的出现了。所以我也调整了它们，遵循与设计翻译相同的规则，并确保它们是一致的。

您将看到消息的格式已经更改:从“%{attribute} %{message}”更改为“%{message}”。现在，我们在翻译后的字符串中引用属性，而不是将它放在字符串的开头，这使得修饰荷兰语消息变得更加容易。我宁愿重复，也不愿忍受有时奇怪的信息。

新的消息格式是在第 5 步中需要向下转换 email 和 password 属性的原因:在错误消息中，它们可以出现在句子中的任何地方。

我还替换了`:taken`消息。设计策略是不透露电子邮件地址是否“未找到”或“无效”。我将该策略扩展到了 Rails 的`taken`消息。

如果你想遵循新的信息风格，请参见[这个文件](https://github.com/F3PiX/Devise-i18n-checklist/blob/master/rails_error_messages)。然后替换原始`nl.yml`文件中 Rails `error: messages:`键的相应部分。一个警告:当您为与设备无关的输入添加验证时，请仔细检查错误消息是否仍然有意义。(我需要进一步调查；我只查了和设计有关的。)

## 第八步:修复我们刚刚打破的标签

在第 5 步中，我们下载了电子邮件和密码翻译，以便在新的错误消息中使用。现在我们需要修复它们在标签中的外观。有几个选项，但我最喜欢的是添加单独的“标签”键。

这为用户键增加了一些重复，但是保持了表单中标签的整洁。

```
# in active_record.nl.yml add the label helper keys:
nl:
  activerecord:
    attributes:
      user:
        current_password: Huidig wachtwoord
        email: e-mailadres
        password: wachtwoord
        password_confirmation: Herhaal het wachtwoord
        remember_me: Wil je ingelogd blijven?
    models: 
      user: Gebruiker
  helpers:
    label:
      user:
        email: Emailadres
        password: Wachtwoord 
```

注意，原来的`nl.yml`中已经有一个`:helpers`作用域。一般来说，将一个作用域的键放在一起是一个好主意，以便于查找(人)。

我们需要做的其他改变很简单。从现在开始不会有大的惊喜出现。🎉。

## 步骤九:更新导航链接

找到所有指向设计(比如导航)的链接，然后用 Rails `t`助手把它们连接起来。

我喜欢把它们放在设计范围的某个地方。yml 的。例如，我将添加`t(".devise.sign_out")`，使`:sign_out`与`:sign_up`和`:sign_in`在同一个范围内(参见示例文件)。

## 第十步:翻译邮件人。

翻译邮件视图。有几个选项:

*   您可以‘i18n’每个字符串和链接名称，就像我们在步骤 6 中对其他视图所做的那样。您可能想要添加一个单独的`mailer.*.yml`。

*   或者您可以为每个地区 : `devise/mailer/reset_password.nl.html.erb`和`devise/mailer/reset_password.en.html.erb`添加单独的邮件程序[视图。每个都有自己的文本。Rails 将选择与所设置的地区相匹配的一个。](https://guides.rubyonrails.org/i18n.html#localized-views)

好吧！现在所有的设计字符串都被正确翻译了。耶！

## 后护理:清理器

在我的视图示例中，我提取了共享密钥(如`:forgot_password`)并将它们收集在通用设备范围内。这使得更改它们更容易，但是 Rails 不能自动找到它们，所以我们需要详细的语法(`t("devise.forgot_password")`)，因为点语法(`t(".new_confirmation_mail")`)不起作用。

如果使用`active_record.*.yml`，那么将活动记录范围内的所有键收集到该文件中是有意义的。将激活的记录键从 Rails `*.yml`文件移动到相应的`active_record.*.yml`中。

我们还有两对复制了`devise:`范围的`*.yml`文件。考虑将设计视图中的键混合到`devise.*.yml`视图中。这就是 device-i18n gem 的作用，我喜欢它。你应该这样做:

```
# mixing the view scopes into devise.nl.yml
nl:
  devise:
    confirmations:
      ...
    registrations:
      ...
      edit:
        ...
      new:
        ... 
```

### 一些调整和提示

*   我不喜欢错误消息中的标题(“x 错误禁止保存该用户”)，所以我从`errors`部分中删除了它。
*   如果您将`default_locale`设置为`:nl`，Rails 将在找不到翻译的情况下“人性化”按键。所以，`t(:some_untranslated_key)`在视图中会显示为`Some Untranslated Key`。
*   使用 [I18n-tasks](https://github.com/glebm/i18n-tasks) gem，您可以清理未使用的密钥并添加丢失的密钥。这是一个伟大的工具！然而，我的 IDE 有很好的 i18n 支持，对我来说，这比 gem 的工作流程要快。

## 离别是如此甜蜜的悲伤

在我们分手之前，最后说几句话。当我开始这段旅程时，我没想到会有原来那么多的工作。我很高兴我现在知道复杂性从何而来。未来的我和你现在有一个需要改变的清单。呜哇！

但是，即使有了这个清单，在你行动之前也要想一想:也许暂时接受次优的翻译是值得的…

客座作者 Maud de Vries 是一名自由职业的 Ruby on Rails 开发人员，是(独立)企业家的蔻驰，她也曾经是一名编辑。里面的作者有时会逃跑。