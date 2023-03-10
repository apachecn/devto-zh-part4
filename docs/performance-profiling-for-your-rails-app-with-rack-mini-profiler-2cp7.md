# 使用 rack-mini-profiler 对您的 Rails 应用程序进行性能分析

> 原文：<https://dev.to/victorhazbun/performance-profiling-for-your-rails-app-with-rack-mini-profiler-2cp7>

无论您是 Ruby on Rails 的新手还是老手，您迟早都会遇到性能问题。

Rack Mini Profiler 帮助您轻松检测**数据库问题**、**内存问题**以及 gems/libs 花费的**时间**。

这个工具被设计成在生产中运行，但是在开发中也能很好地工作。然而，你需要对开发做一些调整。**注意:** Rails 开发模式指定**在每个请求上重载类**，这将对您的概要文件度量产生影响。

我将向您展示如何使用这个工具来检测最常见的 Ruby on Rails 性能问题。

## 安装机架式微型剖面仪

下面再补充一下宝石。

**Gemfile**

```
...

gem 'rack-mini-profiler'
gem 'flamegraph'
gem 'stackprof'
gem 'memory_profiler' 
```

Enter fullscreen mode Exit fullscreen mode

启动服务器——您应该会在左上角看到速度徽章。

现在，我们将让应用程序像在生产环境中一样运行。

首先，将 Rack-mini-profiler 存储改为内存，而不是文件系统:

**config/initializer/rack _ mini _ profiler . Rb**

```
Rack::MiniProfiler.config.storage = Rack::MiniProfiler::MemoryStore 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在本地环境中禁用 SSL:

**配置/环境/生产. rb**

```
config.force_ssl = false 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们运行数据库设置，编译资产并设置密钥库:

```
rails db:reset RAILS_ENV=production 
rails assets:precompile RAILS_ENV=production 
rails s RAILS_ENV=production SECRET_KEY_BASE=secret 
```

Enter fullscreen mode Exit fullscreen mode

## 速度徽章

通过访问根 url 你会看到速度徽章，它会告诉你应用程序渲染花了多少时间。它还显示了你在布局，视图和局部花费的时间。

单击徽章将显示一些详细信息:

**持续时间(毫秒)**，显示您的总请求时间。
**查询**，显示你正在生成多少个查询。
**% in sql** 显示了在 sql 中花费的时间百分比(使用生产数据库副本或类似的东西会比使用开发数据库数据好得多)。
**Dom Content Loaded 事件**显示了从收到响应到完成所有内容的加载之间的时间。(越少越好，优化前端会让这个时间减少)。

现在，让我们检查 SQL 链接，看看我们得到了什么。

您将看到部分呈现时间，以及在查询中花费的时间、查询本身和执行查询的行。如果 SQL 查询开销很大，您可以考虑修复/删除它，或者缓存它来解决问题。有时候可能无非就是少了一个`include`。

## 火焰图

将`?pp=flamegraph`添加到 URL 查询字符串，应用程序将呈现 flamegraph 图表。

`Y`是堆栈级别，而`X`是时间。简单吧？

滚动查看图表中的更多详细信息。

在页面底部，您会看到一些不同颜色的方框，百分比是请求在堆栈框架内花费的时间百分比。

每个应用程序显示不同的指标，视图中可能有一些 gem 助手。

假设`number_to_phone`，你在页面上有 100 条记录在每个分部中调用这个 helper，这将代表渲染每个分部需要更多的时间。一种解决方案是停止使用 Rails 助手，让 JavaScript 来完成这项工作。这样，火焰图就不会抱怨那个慢吞吞的助手所花费的时间。有时你会发现有些助手或方法比你想象的要昂贵，在这种情况下，你可以编写自己的 **light** 实现。

## 垃圾收集剖析

用`rack-mini-profiler`调试生产中的内存问题很简单。让我们找出方法。

### 简介-gc

将`?pp=profile-gc`添加到 URL 查询字符串，将生成一个非常长的输出。结束的时候去喝杯咖啡，这需要时间。:-)

找到在 Ruby heaps 部分之外分配的**新字节。**

如果这个数字大于 8 MB，您可能要开始调查，看看在那个特定的请求中发生了什么，因为有些东西正在消耗大量的内存。

现在，让我们转到由请求引起的**对象空间增量**部分，

假设您有`String : 7300`,这意味着在这个请求之后您还有 7300 个字符串。

接下来是**对象空间统计**部分。

这是虚拟机中按类划分的活动对象总数。你可能会发现列出了`MIME::Type`或`MIME::Types::Container`这是因为在你的`Gemfile`中可能有一个宝石人正在使用旧版本的`ruby-mime-types`宝石。这个问题可以通过在你的 gem 文件中设置一个新版本的`ruby-mime-types`宝石来解决。

最后是**字符串统计数据**部分。

这是字符串被分配的次数。可以通过将这些字符串移动到`frozen` `CONSTANTS`来减少这些数字。假设您在一个请求中分配了 3000 次字符串“hello”。为了解决这个分配问题，我们将“hello”字符串移动到一个常量，现在你只有一个分配给这个字符串，而不是 3000 个。

### 简介-记忆

将`?pp=profile-memory`添加到 URL 查询字符串中，告诉我们哪一行代码是**分配给**一个给定的对象。

我们对“保留的”对象感兴趣(在输出中向下滚动，您会发现“保留的”部分)，这种对象将在请求完成后保留。这是 Ruby 对象容易泄漏的地方。

## 异常缓慢

将`?pp=trace-exceptions`添加到 URL 查询字符串，返回应用程序引发异常的所有地方。

避免使用异常作为流程控制的**形式，因为异常是缓慢的。您可以将异常处理委托给一个类，而不是到处引发异常，这个类会尽快向最终用户返回一些信息。**

**慢:**

```
catch A -> raise B -> catch B -> raice C -> catch C -> return 
```

Enter fullscreen mode Exit fullscreen mode

**快:**

```
catch A -> handle exception & return 
```

Enter fullscreen mode Exit fullscreen mode

> 有时候，快速的选择是不可能的，但是你应该永远以此为目标。不应该有例外，想想吧...现在跟我重复一遍:“我不会到处引发异常来驱动我的 Ruby 应用程序”。

## 结论

Rack-mini-profiler 提供了不同的工具，供您调试和分析基于机架的应用程序，如 Rails。既然你对每个工具都有所了解，你应该能够开始修复你一直试图修复的那个缓慢的页面了。

## 最后的想法和下一步

在生产和开发环境中添加 rack-Mini-profiler 将有助于您轻松解决性能问题，您已经学习了如何在开发中设置它，请记住，您的开发环境必须**像您的生产环境一样运行，或者至少非常相似**。

使用 rack-mini-profiler 查看页面生成的查询，确保没有使用 Ruby 异常控制应用程序流。大多数常见的 Rails 问题都与缓慢的 SQL 查询、来自 Rails 应用程序的大量响应、等待第三方 API、缺少后台作业有关，并且这个列表可以无限扩展。

现在，您应该准备好解决常见的问题，然后，当您掌握 rack-mini-profiler 时，解决任何类型的性能问题都将很容易。