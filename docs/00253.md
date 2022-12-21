# 用 define_method 组织银行/银行 gem 的授权

> 原文：<https://dev.to/n350071/organize-the-authorizations-of-the-banken-pundit-gem-with-definemethod-2gei>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的 Rails 笔记

### n350071🇯🇵9 月 11 日 193 分钟阅读

#rails](/n350071/my-rails-note-47cj)

[银行](https://github.com/kyuden/banken)是我不知道我是说，

> 受权威人士的启发，为 Rails 设计了一个简单而轻量级的授权库。Banken 提供了一组助手来限制给定用户可以访问的资源。

## 🤔情况

授权会随着你的 app 成长，会是这样的。

新增？指秀？，秀？参考索引？。

```
class BooksLoyalty < ApplicationLoyalty
  def index?
    @user_context.user == @record.user
  end
  def show?
    index?
  end
  def new?
    show?
  end
end 
```

## 👍解决办法

因为每个方法都是一样的，所以它们不会转移它们的逻辑。然后，`define_method`可能就好听了。

```
class BooksLoyalty < ApplicationLoyalty
  [:index?, :show?, :new?].each do |method|
    define_method method do
      @user_context.user == @record.user
    end
  end
end 
```

如果需要参数，另请参见📚[我的 github 笔记](https://github.com/n350071/notes/blob/7f435d60e76acece1ec46efb3fa1d8586e307640/Ruby/define_method.rb)。