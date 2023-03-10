# Rails 6 中的新框架默认值

> 原文：<https://dev.to/prathamesh/new-framework-defaults-in-rails-6-4ph9>

Rails 6 发布了。这篇文章是 _ **Road to Rails 6** _ 系列的一部分，它将帮助你为 Rails 6 做好准备。

每次新的主要 Rails 版本发布时，都会有大量的突破性变化。将现有应用程序升级到新版本肯定会因为这些变化而破坏某些代码。但是从 Rails 5 开始，Rails 在一个名为`new_framework_defaults.rb`的文件的帮助下，使得采用大变革变得更加容易

> 在更新 Gemfile 以使用 Rails 6 之后运行`rails app:update`任务时，会创建`new_framework_defaults`文件。如果您不运行更新任务，则不会创建它。运行更新任务极其重要，因为它还会更新其他一些配置。

对于每个新的主要 Rails 版本，都会为框架默认值创建一个单独的文件。对于 Rails 5.2 是`new_framework_defaults_5_2.rb`。对于轨道 6，它是`new_framework_defaults_6_0.rb`。如果你把你的应用升级到 Rails 6，你会在`config/initializers`下找到`new_framework_defaults_6_0.rb`。

该文件有助于根据代码库的复杂性，按照您的步调采用这些突破性的特性。如果你打开这个文件，你会发现里面的所有东西都被注释掉了。

```
# Don't force requests from old versions of IE to be UTF-8 encoded.
# Rails.application.config.action_view.default_enforce_utf8 = false
...
# Enable the same cache key to be reused when the object being cached of type
# `ActiveRecord::Relation` changes by moving the volatile information (max updated at and count)
# of the relation's cache key into the cache version to support recycling cache key.
# Rails.application.config.active_record.collection_cache_versioning = true 
```

Enter fullscreen mode Exit fullscreen mode

迷茫？别担心。在这篇文章中，我们将回顾这个文件的内容，看看如何在您的应用程序中采用这些特性。

> 如果您正在创建一个全新的 Rails 6 应用程序，那么就不会创建这个文件。因为 Rails 确保所有主要的中断特性都是默认启用的。

### 特征标志

大多数时候，每一个新的重大变化都隐藏在一个特性标志的后面。这使得在确保您的应用程序代码按照重大变更进行了更改之后，采用某个特性变得更加容易。例如，让我们举一个 Rails 6 中集合缓存改进的例子。Rails 6 通过将缓存键的变化部分(比如更新的时间戳和计数)移动到缓存版本中，使得重用`ActiveRecord::Relation`对象的缓存键成为可能。这个变化是由`Rails.application.config.active_record.collection_cache_versioning`控制的。

> Rails 将这些特性标志称为**框架默认值**，并将它们全部放在`new_framework_defaults_6_0.rb`文件中。

对于新的 Rails 6 应用程序，默认情况下启用这一更改，并且配置设置为 true。

对于升级到 Rails 6 的旧应用程序，该设置被设置为`nil`，并且在`new_framework_defaults_6_0.rb`中提供了一行，以便在您想要开始使用该功能时启用它。

```
# new_framework_defaults_6_0.rb

# Enable the same cache key to be reused when the object being cached of type
# `ActiveRecord::Relation` changes by moving the volatile information (max updated at and count)
# of the relation's cache key into the cache version to support recycling cache key.
# Rails.application.config.active_record.collection_cache_versioning = true 
```

Enter fullscreen mode Exit fullscreen mode

现在你会明白为什么所有的台词都在`new_framework_defaults_6_0.rb`里被注释了。Rails 希望您在查看了更改对代码的影响之后，能够仔细启用新的默认设置。

### 包含所有新的框架默认值

您已经准备好使用所有新的框架默认值，然后只需删除`new_framework_defaults_6_0.rb`文件并更改`config/application.rb`中的下一行

```
# Initialize configuration defaults for originally generated Rails version.
# config.load_defaults 5.2
config.load_defaults 6.0 
```

Enter fullscreen mode Exit fullscreen mode

最初，它被设置为从 Rails 5.2 加载默认设置，以便应用程序升级到 Rails 6。现在，我们已经更改为从 Rails 6 加载默认值。

> 对于新的 Rails 6 应用程序，该设置已经设置为从 6.0 加载默认值。

### 添加新的默认配置

如果你要给 Rails 添加一个突破性的改变，那么你应该如何做呢？

> 接受 Rails 贡献者的帮助，他们会审查您的代码。他们会引导你在`new_framwork_defaults`中为旧应用添加合适的默认值。

新框架的默认结构使得 Rails 团队可以很容易地引入必要的突破性变化，而不会影响旧应用程序升级过程的简易性。

### 想留在通往 Rails 6 的路上？

订阅我的[时事通讯](https://www.prathamesh.tech/road-to-rails-6)，每周收到一封电子邮件，了解如何让你的应用为 Rails 6 做好准备。