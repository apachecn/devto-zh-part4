# 甚至更好的 Rails 日期格式

> 原文：<https://dev.to/dlains/even-better-rails-date-formatting-2742>

我最近偶然看到了安迪·克罗尔的文章关于使用`strftime`在 Rails 视图中格式化日期和时间值的文章。

他提出了一个有力的论点，即在 Rails 视图中不应该使用`strftime`:

```
<%= @user.last_sign_in_at.strftime('%m-%e-%y %H:%M') %> 
```

因为 Rails 内置了日期和时间格式，您也可以定义自己的格式。要使用一种内置格式，你可以调用`Date`或`Time`类上的`to_s`，并传入你想要使用的格式:

```
<%= @user.last_sign_in_at.to_s(:long) %> 
```

见安迪·克罗尔的文章了解更多细节。

## 那`nil`呢？

如果你花了很多时间编写 Ruby 或 Rails 代码，你可能已经发现了上面的`to_s`调用的问题。如果`last_sign_in_at`的值是`nil`呢？

你可能会想...“嗯，没问题。在`nil`上调用`to_s`只是返回一个空字符串，所以没有伤害，没有犯规。”

```
nil.to_s
  => "" 
```

这个思路有两个问题。首先，Rails 实际上创建了一个`to_formatted_s`方法，它接受一个引用要使用的格式的参数。对于`Date`和`Time`对象来说，`to_formatted_s`方法也是`to_s`的别名。如果你试图传递一个参数给`to_s`的`nil` s 版本，你会得到一个错误。

```
nil.to_s(:long)
ArgumentError (wrong number of arguments (given 1, expected 0)) 
```

第二，您实际上可能希望有一些可视化的表示，表明日期或时间丢失或未设置。当缺少日期或时间时，我喜欢显示几个破折号。

## 两全其美

下面是我如何在我的 Rails 应用程序中处理日期和时间格式。

在`application_helper`模块中，我添加了一个`format_date`方法，因此它对每个视图都可用。

```
def format_date(date, format = :long)
  date.blank? ? '--' : date.to_s(format)
end 
```

在你看来你可以这样称呼它:

```
<%= format_date @user.last_sign_in_at %> 
```

如果用户从未登录，您将看到两个破折号，表示没有设置`last_sign_in_at`时间。否则`last_sign_in_at`将被格式化为`:long`格式。

如果你想覆盖默认格式，你可以随时传入不同的格式。

```
<%= format_date @user.last_sign_in_at, :db %> 
```

或者使用您自己的格式(前提是您定义了一种如 Andy Croll 的文章中所概述的格式)。

```
<%= format_date @user.last_sign_in_at, :myformat %> 
```

## 结论

注意你代码中的`nil` s。每当你在代码中看到一个`.`的时候，想想自己，“如果那个`.`左边的东西是`nil`，会发生什么？”。

*最初发布于[davidlains.com](https://davidlains.com)。*