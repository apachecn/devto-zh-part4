# Rails 5.2.x - > Rails 6 计数器 _ 缓存

> 原文：<https://dev.to/loribbaum/rails-5-2-x-rails-6-countercache-gotcha-3bgc>

我最近将我们的生产应用从 Rails 5.2.3 升级到了 Rails 6.0.0。我们想接触到所有人都在谈论的闪亮的东西。多个数据库！更好的自动加载！

一切都很顺利，直到我注意到一个奇怪的行为，count 列没有更新，但是所有的关联都在正确地更新。在与一位同事讨论了这个问题后，我们确定了发生了什么。罪魁祸首？Rails ActiveRecord `counter_cache`。

这篇文章将简要概述`counter_cache`以及它的行为在两个 Rails 版本之间是如何变化的，最后，如何更新你的代码来解决这个问题而不把`counter_cache`扔出窗外。

### 计数器缓存——是什么？我为什么要用它？

这个概念对我来说是全新的——太棒了！我喜欢学习新事物，并努力理解为什么这种方法比其他方法更好。在这种情况下，它是旧的代码，我们不能问最初的作者为什么他们做出这个决定。所以我去了互联网。

我找到了不少关于如何实现`counter_cache`的文章，但惊讶地发现没有一篇来自官方的 Rails 文档——这很有趣。

也就是说，我认为我找到的最好的资源是[这一集(非常老的)Railscast】关于转换获取记录计数及其与数据库中计数器缓存的关系。我强烈推荐观看它——但是这里有一个简短的回答，说明为什么您会选择实现计数器缓存:](http://railscasts.com/episodes/23-counter-cache-column)

> 计数器缓存列将关联的计数存储在自身上——这意味着您不必进行额外的数据库调用=更高的性能

### 旧行为&实现

假设我们有一个饼干罐，每个饼干罐里面都有很多饼干。在我们的 UI 中，我们希望显示每个 cookie jar 中有多少 cookie。

建立我们的模型的一个简单实现可能如下所示:

```
# cookie_jar.rb
class CookieJar
  has_many :cookies
end

# ## Schema Information
# Table name: `cookie_jars`
# ### Columns
#
# Name                 | Type               | Attributes
# -------------------- | ------------------ | ---------------
# **`id`**             | `bigint(8)`        | `not null, primary key`
# **`cookies_count`**  | `integer`          | `default(0), not null`

# cookie.rb
class Cookie
  belongs_to :cookie_jar, counter_cache: true
end

# ## Schema Information
# Table name: `cookies`
# ### Columns
#
# Name                 | Type               | Attributes
# -------------------- | ------------------ | ---------------
# **`id`**             | `bigint(8)`        | `not null, primary key`
# **`cookie_jar_id`**  | `integer`          | `not null` 
```

这意味着我们现在有了`CookieJar`、`Cookie`、`cookie_jars`表中有一个名为`cookies_count`的列，它使用`counter_cache`来计算这些值。

所以现在棘手的部分- `counter_cache`是在幕后做一些工作，这使得它易于使用，但调试起来更具挑战性。在这种情况下，我们的 bug 来源于一些种子代码，如下所示:

```
# seed_cookie_jar.rb
def assign_cookies
  jar = CookieJar.first
  jar.cookies.each do |cookie|
    cookie.update!(cookie_jar_id: jar.id)
  end
end 
```

*需要注意的是，在这个例子中，我们不要担心我们的 cookies 是在哪里创建的——只是它们之前有一个不同的`cookie_jar_id`,我们希望在这里的`assign_cookies`中进行更改。*

因此，在 Rails 6 之前，这种行为是有效的——我将我的第一个 cookie 从一个 jar 移动到另一个 jar，旧的 jar 现在少了一个`cookies_count`,新的 jar 增加了它的`cookies_count`。

调用`assign_cookies`之前的 Cookie Jar 表

| 身份证明（identification） | cookies _ count |
| --- | --- |
| one | four |
| Two | Zero |

移动饼干后，我们的饼干罐桌子是什么样子

| 身份证明（identification） | cookies _ count |
| --- | --- |
| one | Zero |
| Two | four |

一切都在按预期工作！我们所有的 4 块饼干都从饼干罐 1 移到了饼干罐 2。令人愉快。现在，让我们破坏这一点，并升级到 Rails 6💪

### 新行为&做出了改变

公平地说，有一行代码引用了实现新行为的 pull 请求。然而，让我感到惊讶的是，变更日志中表述变更的方式根本没有提醒我，我的代码现在基本上被破坏了。

下面是 changelog 注释:“除非记录被实际保存，否则不要更新计数器缓存”。是的，就是这样。现在，我不是 Ruby 或 Rails 的专业人士，所以对其他人来说这可能是显而易见的行为...但是天啊，我兜了一圈！

在没有对我们的代码做任何事情的情况下，这里是我们的 bug:
我们在调用`assign_cookies`之前的 Cookie Jar 表(和上次一样)

| 身份证明（identification） | cookies _ count |
| --- | --- |
| one | four |
| Two | Zero |

我们的 Cookie Jar 表在我们调用`assign_cookies`之后看起来是什么样子——没有错误，但是那些计数看起来不对🤔

| 身份证明（identification） | cookies _ count |
| --- | --- |
| one | four |
| Two | Zero |

更令人困惑的是，移动 cookie 后，我们的 cookie 表是什么样子:

| 身份证明（identification） | cookie_jar_id |
| --- | --- |
| one | Two |
| Two | Two |
| three | Two |
| four | Two |

什么？！我们的饼干居然动了罐子，但是计数器没有更新。因此，在我们的 UI 中，我们显示 Cookie Jar 1 中有 4 个 Cookie，但是当您单击查看 Cookie Jar 1 的详细信息时，它是空的！

老实说，我和我的同事发现这一点的方法是在更改日志中列出的拉请求中更改代码，查看代码差异，新的测试案例，并尝试一些东西。所有这些最终导致我们尝试一件事:不是传递 id 值，而是将整个记录传递给块。(剧透警告——这个管用！)下面是代码:

```
# seed_cookie_jar.rb
def assign_cookies
  jar = CookieJar.first
  jar.cookies.each do |cookie|
    cookie.update!(cookie_jar: jar)
  end
end 
```

所以不要再直接设置了——让我们将整个 jar 实例传递给它，看看会发生什么。事实证明，这确实调用了对`jar`实例的保存并触发了缓存更新。

* * *

感谢你坚持到现在——希望你学到了新的东西，并且现在对更新关联`_id`字段更加谨慎！从现在开始，在传入一个显式的`_id`值和实例之前，我肯定会三思而行。

* * *

布鲁克·拉克在 Unsplash 上的照片