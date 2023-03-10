# Ruby 即时性能测量

> 原文：<https://dev.to/n350071/ruby-instant-performance-measurement-2kcp>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的 Rails 笔记

### n350071🇯🇵9 月 11 日 193 分钟阅读

#rails](/n350071/my-rails-note-47cj)

## 🤔情况

让我们假设你有这样的代码，你想比较实例和会话的性能。

```
def current_role
  return @current_role if @current_role
  # ommit..
  @current_role = session[CURRENT_ROLE].reload
end 
```

## 🦄即时解决方案

你可以用这个代码来衡量。

```
def instant_performance(&target)
  start_time = Time.now
  yield
  Time.now - start_time
end 
```

## 👍用法&举例

```
> instant_performance{ puts 'hello' }
hello
=> 3.9e-05

> instant_performance{ puts 'hello' }.class
hello
=> Float

> instant_performance{ @user }
=> 2.0e-06

> instant_performance{ @current_role }
=> 3.0e-06

> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }
=> 41.0

> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }
=> Infinity

> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }
=> 44.0

> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }
=> 42.0

> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }
=> 29.0

> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }
=> 56.0

> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }

=> Infinity
> instant_performance{ user_session[CURRENT_ROLE] } / instant_performance{ @current_role }
=> 50.00000000000001
`` 
```