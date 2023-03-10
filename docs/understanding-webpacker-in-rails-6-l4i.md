# 理解 Rails 6 中的 Webpacker

> 原文：<https://dev.to/prathamesh/understanding-webpacker-in-rails-6-l4i>

从 Rails 6 开始，Webpacker 就是默认的 JavaScript 编译器。这意味着所有的 JavaScript 代码都将由 Webpacker 处理，而不是旧的资产管道，即链轮。Webpacker 在理念和实现上都不同于资产管道。在这篇博文中，我们将了解 Webpacker 如何处理 JavaScript。

### 什么是 Webpacker

`webpacker`是一个 gem，它封装了`webpack` - *，这是一个流行的 JavaScript 工具，用于管理和捆绑 JavaScript 代码* -并提供助手在我们的 Rails 应用程序中使用 webpack。简而言之，它提供了 Rails 使用 Webpack 的方式。对于一个非常强大的工具来说，这是一个非常简单的定义，但对我们来说已经足够了。

Webpacker 将 webpack 封装在一个 Ruby gem 中，并提供助手在 Rails 应用程序中使用 Webpacker 的输出。

当您创建一个新的 Rails 6 应用程序时，您将在控制台中看到以下输出。

```
 rails webpacker:install
RAILS_ENV=development environment is not defined in config/webpacker.yml, falling back to production environment
      create config/webpacker.yml
Copying webpack core config
      create config/webpack
      create config/webpack/development.js
      create config/webpack/environment.js
      create config/webpack/production.js
      create config/webpack/test.js 
```

Enter fullscreen mode Exit fullscreen mode

你也会默认在`Gemfile`里找到`webpacker`宝石。`rails new`命令也将通过`yarn`安装成吨的 npm 包。

> 默认情况下，升级到 Rails 6 的旧应用程序不会安装`webpacker` gem。您需要手动将其包含在 Gemfile 中，然后运行`rails webpacker:install`。我们将在后面的博文中介绍如何在现有的 Rails 应用程序中使用 Webpacker。

### JavaScript 代码的新目的地

在 Rails 6 之前，所有的 JavaScript 代码都应该在`app/assets/javascripts`中。但是在一个 Rails 6 应用中，`app/assets/javascripts`目录甚至不存在。相反，我们有`app/javascript`目录来托管所有的 JavaScript 代码。这个名字包含了应用程序的 JavaScript 部分，可以是应用程序前端的所有代码。

让我们在一个空的 Rails 6 应用程序中浏览一下这个目录的内容。

```
Projects/scratch/better_hn master ✗ 2.6.3 ◒
▶ tree app/javascript
app/javascript
├── channels
│   ├── consumer.js
│   └── index.js
└── packs
    └── application.js

2 directories, 3 files 
```

Enter fullscreen mode Exit fullscreen mode

它包含两个目录，`channels`和`packs`。`channels`目录由 Rails 的 Action Cable 组件生成。我们现在可以安全地忽略它。`packs`目录对我们很重要，所以让我们看看它包含了什么。

```
// app/javascript/application.js
require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels") 
```

Enter fullscreen mode Exit fullscreen mode

### 什么是包？

Webpack 有一个入口点的概念，入口点是 Webpack 在开始编译 JavaScript 代码时首先寻找的文件。Webpacker gem 在`app/javascript/packs`下以这个`application.js`文件的形式创建`application`包。如果您记得资产管道，这个文件相当于`app/assets/javascripts/application.js`文件。

Rails 生成的`application`包包含与 Rails 组件相关的代码，如 trubolinks、Active Storage 和 Action Cable。

> 你会注意到，所有包含 JavaScript 组件的 Rails 框架，如 Rails-UJS、Turbolinks、Active Storage，都被迁移到支持 Webpacker。即使是 Rails 6 中引入的新框架，如 Action Text，也可以与 Webpacker 一起工作。

所以`application`包是所有 JavaScript 代码的入口点。我们可以创建自定义包，并把它们放在`app/javascript/packs`目录中，Webpacker 在编译时会很高兴地找到它们。

该设置由 Webpack 在文件`config/webpacker.yml`中为我们配置

```
# config/webpacker.yml
5: source_entry_path: packs 
```

Enter fullscreen mode Exit fullscreen mode

如果我们希望 Webpack 为 JavaScript 代码寻找额外的目录，我们可以在`config/webpacker.yml`中配置设置`resolved_paths`。就配置选项而言，该文件非常简单明了。

### 编译 JavaScript 代码

下一步自然是研究如何使用 Webpacker 和 webpack 编译 JavaScript 代码。在开发模式下，您不必做任何事情。当您运行 rails 服务器时，编译发生在请求期间，类似于它过去处理资产管道的方式。

#### 使用 webpack-dev-server 进行实时重装

Webpacker 生成一个文件`bin/webpack-dev-server`，可以用于开发阶段的实时重载。为此，我们必须单独运行 webpack-dev-server，然后我们就可以看到实时重载和热模块替换在起作用。

#### 生产模式

在生产中，webpacker 将`webpacker:compile`任务附加到`assets:precompile`任务上。因此，如果您的构建管道运行`assets:precompile`任务，它也会负责编译由 webpack 编译的文件。由于 wepack 包是`package.json`的一部分，yarn 将负责安装它，以便它可以编译 JavaScript 代码。

### 包括应用程序中的 JavaScript 代码

我们已经看到了如何使用 webpacker 编译 JavaScript 代码，但是如何将它包含在我们的应用程序中呢？

为此，Webpacker 提供了一个助手方法`javascript_pack_tag`。顾名思义，我们使用它将 webpacker 包包含在我们的布局文件中。这相当于资产管道中的`javascript_link_tag`。

```
# app/views/layouts/application.html.erb

<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %> 
```

Enter fullscreen mode Exit fullscreen mode

`javascript_pack_tag`负责确保它在开发模式以及类似于资产管道的生产模式中正确地引用已编译的资产。

* * *

今天到此为止。我们大致了解了 Webpacker 如何让我们在 Rails 6 应用程序中使用 webpack，并且了解了 Webpacker 世界中的`packs`。我们还看到了编译是如何发生的，以及如何在应用程序中使用编译后的代码。在下一篇文章中，我们将讨论 Webpack 哲学，以及作为 Rails 开发人员使用这种新的 JavaScript 代码编写方式意味着什么。

如果你想了解更多关于如何在 Rails 6 中使用 webpack 和 Webpacker 来编写现代 JavaScript 的信息，请订阅我的时事通讯 [Road to Rails 6](https://prathamesh.tech/road-to-rails-6) ，在那里我不仅会写关于 Webpacker 的内容，还会写很多其他与 Rails 6 相关的文章。