# Rails 中面向装饰者的重构

> 原文：<https://dev.to/victorhazbun/refactoring-towards-decorators-4kj2>

### 装修工

它们包装对象并扩展其功能，而不修改对象本身。

### 问题

假设你有以下观点。

```
 <p>Name: <%= @company.name.titleize %></p>

 <p>Email: <%= @company.email_private? 'Private' : @company.email %></p>

 <p>Sign Up At: <%= @company.sign_up_at.strftime('%d. %m. %Y.') %></p> 
```

Enter fullscreen mode Exit fullscreen mode

这是不好的，因为视图不应该关心如何显示数据，它应该只显示数据。

### 装饰者对象

```
class CompanyDecorator < SimpleDelegator
  def display_name
    name.titleize
  end

  def protected_email
    return "Private" if email_private?
    email
  end

  def formatted_sign_up_at
    sign_up_at.strftime("%d %b %Y")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 实现

很好，现在让我们看看控制器是什么样子。

```
class CompanyController < ApplicationController
  def show
    company = Company.find(params[:id])
    @decorated_company = CompanyDecorator.new(company)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，查看

```
 <p>Name: <%= @decorated_company.display_name %></p>

 <p>Email: <%= @decorated_company.protected_email %></p>

 <p>Birthday: <%= @decorated_company.formatted_sign_up_at %></p> 
```

Enter fullscreen mode Exit fullscreen mode

### 最后的想法

不仅看起来更好，测试这种方法也更简单。

这不仅适用于 Rails 应用程序，这种模式可以用在 Ruby 的任何地方。