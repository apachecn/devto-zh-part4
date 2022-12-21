# 如何为改革属性使用 Rails 翻译

> 原文：<https://dev.to/jetrockets/how-to-use-rails-translations-for-reform-attributes-2j3k>

如果您对表单对象使用 [Reform](https://github.com/trailblazer/reform) ,并希望 Rails 应用程序中的翻译与 ActiveRecord 对象一样工作，那么您可以将`ActiveModel::Translation`模块添加到表单类或基类中，并使用翻译的键指定方法。

```
class BaseForm < Reform::Form
  extend ActiveModel::Translation

  def self.i18n_scope
    :forms
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
class UserForm < BaseForm
  feature Coercion

  property :login
  property :change_password, virtual: true, type: Types::Form::Bool
  property :password
  property :password_confirmation

  validation do
    required(:login).filled
  end

  validation if: ->(_) { change_password } do
    required(:password).confirmation
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为表单域设置翻译。
您可以将它们移动到一个新文件`config/locales/forms.yml`。

```
en:
  forms:
    attributes:
      user_form:
        login: Enter login
        change_password: Change password?
        password: Enter password
        password_confirmation: Confirm password 
```

Enter fullscreen mode Exit fullscreen mode

现在，简单表单将使用翻译，或者您可以通过`UserForm.human_attribute_name`手动调用它们。