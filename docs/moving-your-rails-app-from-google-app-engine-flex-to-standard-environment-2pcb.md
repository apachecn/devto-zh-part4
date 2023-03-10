# 将你的 Rails 应用从 Google App Engine Flex 迁移到标准环境

> 原文：<https://dev.to/cyclotron3k/moving-your-rails-app-from-google-app-engine-flex-to-standard-environment-2pcb>

**TL；标准环境的默认 GAE 配置阻止 Rails 应用程序运行。因为`skip_files`阻止了一个关键文件的上传。**

Google App Engine 目前有两种环境:标准和 Flex。无需深入细节，Flex 环境更加灵活，可以运行 docker 容器中运行的任何东西。标准环境更具限制性(将您限制在特定的语言范围内)，但它也有其他好处，比如更快的部署时间(大约 4 分钟而不是大约 15 分钟)，更快的扩展，甚至可以扩展到零。

直到最近，标准环境还没有对 Ruby 爱好者开放，但在 2019 年 6 月的[日，谷歌宣布推出 Ruby 2.5 运行时。](https://cloud.google.com/appengine/docs/standard/ruby/release-notes)

## 为什么从 Flex 迁移到 Standard？

通过将 Ruby 项目从 Flex 迁移到 Standard，您可以获得以下好处:

*   可能会便宜很多
*   部署速度从大约 15 分钟提高到大约 4 分钟
*   更快的扩展
*   可以扩展到零，即零成本

注:看看[对比页](https://cloud.google.com/appengine/docs/the-appengine-environments)就知道是否适合自己。

## 迁移到标准

要从 Flex 迁移到 Standard，您需要做一些更改。

### 仅 Ruby 2.5

您可能已经在 Flex 环境中使用了 Ruby 2.6，或者您的应用程序正在使用一个旧版本的 Ruby，但是标准环境只提供了 Ruby 2.5，没有其他的。您需要确保您的应用程序可以在 2.5 环境中运行。这意味着如果你正在使用 2.6 中的任何[新特性，那么你需要把它们去掉。](https://www.rubyguides.com/2018/11/ruby-2-6-new-features/)

这是一份清单:

*   删除`.ruby-version`
*   如果您在 Flex 中使用 2.6，请确保您没有使用任何仅在 2.6 中出现的功能
*   您的`Gemfile`可能会指定一个类似于`ruby '~> 2.6.2'`的 Ruby 版本。你需要把它改成`ruby '~> 2.5'`

### 更新 app.yaml

三个主要要求是:

*   移除`env: flex`
*   将`runtime: ruby`替换为`runtime: ruby25`
*   提供`skip_files`配置(见下文)

([完整的 app.yaml 文档](https://cloud.google.com/appengine/docs/standard/ruby/config/appref))

### 跳过 _ 文件

在你的`app.yaml`中，有一个参数`skip_files`告诉`gcloud app deploy`哪些文件不要上传到 GAE。如果您没有覆盖`skip_files`(您可能没有)，它将使用一组默认的排除模式，并阻止任何`dot-files`被上传。这通常是一件有用的事情，但是它会破坏任何 Rails 部署，因为它阻止了链轮清单文件的上传。这会导致您的应用程序抛出`The asset "somefile.jpg" is not present in the asset pipeline.`错误，这可能会将您带到这个页面。

默认的`skip_files`配置如下:

```
# BAD
skip_files:
- ^(.*/)?#.*#$
- ^(.*/)?.*~$
- ^(.*/)?.*\.py[co]$
- ^(.*/)?.*/RCS/.*$
- ^(.*/)?\..*$ 
```

正是这最后一行阻止了`public/assets/.sprockets-manifest-5y483543959430890.json`的上传，从而导致 Rails 认为资产没有被预编译。

您需要通过向`app.yaml` :
添加如下内容来覆盖它

```
# GOOD
skip_files:
- ^(.*/)?#.*#$
- ^(.*/)?.*~$
- ^(.*/)?.*/RCS/.*$
- ^(.*/)?\.git/.*$ 
```

[阅读文档](https://cloud.google.com/appengine/docs/standard/python/config/appref#skip_files)了解标准环境下`app.yaml`格式的更多信息。注意(截至 2018-08-19)关于`skip_files`的部分只出现在 Python 版本的文档中，而没有出现在 Ruby 中，尽管它与两者都相关。