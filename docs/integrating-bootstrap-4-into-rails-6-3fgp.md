# 将引导程序 4 集成到 Rails 6 中

> 原文：<https://dev.to/rob__race/integrating-bootstrap-4-into-rails-6-3fgp>

为了安装 Bootstrap 4 和一个主题，我们将添加一些包，更新一些配置，并添加一些额外的 gems 库，以使开发变得更容易。假设你已经用`rails new app_name`启动了一个应用

* * *

注意:本教程摘自我即将出版的书[在 Rails 6](https://buildasaasappinrails.com) 中构建一个 SaaS 应用中的一章。这本书通过将应用程序部署到生产中来指导你从卑微的开始。这本书现在已经开始预售了，你现在就可以拿到免费的一章！

* * *

首先要做的是添加另一个版本管理器！因为我们使用的是 Yarn/NPM/Node，所以管理特定应用程序或目录将要使用的节点版本是一个好主意。为此，我们将使用 NVM，您可以用下面的代码行安装 NVM。如果你是一个看 github repo 或安装脚本的人，你可以点击这里。

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash 
```

这一行从 Github 获取安装脚本并作为 Bash 运行。

现在，Node 的当前 LTS(长期稳定)版本是版本 10。OSS(开源)社区中的 LTS 版本是您可以长期依赖的版本。使用以下命令安装节点:

```
nvm install 10 
```

该行安装版本 10 的最新更新版本。这意味着，随着小的更新和错误修复被发布到版本 10 管道中，它们将版本号提高到 10 以下(即 10.3.4 -> 10.3.5)。安装完成后，您可以通过以下命令告诉您的终端窗口/窗格使用 node 10:

```
nvm use 10 
```

当你创建新的应用程序时，Rails 应该已经确保添加了`brew`和`yarn`。因此，您现在应该能够安装 AdminLTE、Bootstrap 和其他一些使 AdminLTE 在您的应用程序中启动和运行所需的依赖项。

首先，要安装与 Bootstrap 4 兼容的 AdminLTE 版本，我们需要在特定的 Github 分支上安装 through Yarn。

```
yarn add https://github.com/ColorlibHQ/AdminLTE#v3-dev 
```

这个命令是说，将 AdminLTE 安装到远程 Github repo `ColorlibHQ/AdminLTE`并使用`v3-dev`。接下来，我们还要安装 Bootstrap、jQuery 和 Popper.js 的包，以确保它们对 Rails 应用程序可用:

```
yarn add bootstrap jquery popper.js 
```

这应该可以确保我们需要的所有主题包都在那里，但是看起来 Rails 并没有完全安装 Webpacker 启动和运行所需的所有东西。为了解决这个问题，让我们运行 Webpacker 安装程序。

```
rails webpacker:install 
```

* * *

好了，我们就要完成设置了！然而，我们应该稍微偏离一下 Webpacker。

Webpacker 是围绕 Javascript 工具 Webpack 的 Ruby(主要是 Rails)包装器。Webpack 是一个工具，允许你在 Javascript 中预处理、捆绑和使用 ES6 类语法。然后，Webpacker 利用所有这些特性，开始将 ES6 Javascript 世界与 CSS 和其他静态资产的资产管道相融合。

Webpacker 引入了“包”的概念，这些包是 ES6 语法的 Javascript 文件，您可以使用`pack_tag`助手将它们包含在任何模板中。可以把包看作 javascript 文件在 Rails 应用程序中特定位置的入口点。

最基本的模式是`/app/javascript/application.js`包文件，它默认包含在应用程序的布局文件中。

随着应用程序的增长，使用包在应用程序的特定模板或部分中指定特定的库和功能有助于确保 Javascript 保持模块化和条块化。

## 运行 Webpack 开发服务器

如果你使用过 Heroku 或其他主机供应商，你可以参考`Procfile`。一个`Procfile`最简单的定义是，它允许你指定一系列不同的过程类型，并让另一个系统(Heroku，Foreman gem 等。)处理启动和关闭。允许在一行和一个终端会话/选项卡/窗格中处理所有这些。

在本地开发时，最容易采取的步骤是使用 gem `Foreman`。要添加此宝石，请将其添加到您的`Gemfile`和`bundle install` :
中的开发组

```
...
group :development do
  gem 'foreman'
... 
```

添加后，您可以将您的`Procfile`添加到应用程序的文件夹结构的根目录。意思是和你的`Gemfile`一个级别。

```
web: bundle exec puma -t 5:5 -p ${PORT:-3000} -e ${RACK_ENV:-development} 
```

你可能会注意到，你看到的不是`rails s`，而是`bundle exec puma`。这种变化是由于您希望在大多数环境中直接运行 Puma。

如果您要用 Foreman 和这个 procfile 启动您的环境，您将在命令行上使用下面的代码:

```
foreman start 
```

该命令为 Procfile 中的每一行启动一个进程。因此，在这种情况下，Puma 的进程运行 Rails 服务器。然而，如果您访问您的应用程序实例，首先您可能会注意到它现在位于 [http://localhost:5000](http://localhost:5000) ，并且它也不输出开发日志。

那么，如何才能让开发使用`rails s`来保留花哨的日志呢？我们可以创建一个仅用于开发的辅助`Procfile.dev`。

```
web: bin/rails s -p ${PORT:-3000}
webpacker: ./bin/webpack-dev-server 
```

## 什么是`webpack-dev-server`？

很简单，它是一个在后台运行的服务器，允许你开发你的 Javascript，手动自动重新加载，并确保你的开发 Rails 服务器提供包给你。

要运行这个 Procfile，只需在命令行语法中添加一个字段:

```
PORT=3000 foreman start -f Procfile.dev 
```

好了，我们就要让一切正常运行了。我们正在着手向 Webpack javascript 配置添加一些东西。

```
// config/webpack/environment.js
const { environment } = require('@rails/webpacker')
const webpack = require('webpack')
// Add an additional plugin of your choosing : ProvidePlugin
environment.plugins.prepend('Provide', new webpack.ProvidePlugin({
    $: 'jquery',
    JQuery: 'jquery',
    jquery: 'jquery',
    'window.Tether': "tether",
    Popper: ['popper.js', 'default'], // for Bootstrap 4
  })
)
module.exports = environment 
```

其中一个新增内容是 8 行插件代码，用于确保 jQuery、Popper.js 和 Tether 在捆绑的 javascript 中可用。

当向 Rails 应用程序添加 javascript 时，整个应用程序中都有您想要的 Javascript 库、设置或变量。在 Rails 6 和 Webpacker 的世界中，这些项目放在位于`/app/javascript/application.js`的应用程序包中。好消息是，当您使用 Rails 命令创建一个新的 Rails 应用程序时，会创建这个文件的一部分内容。让我们检查一下添加的内容，以满足管理员的需求。

```
/ This file is automatically compiled by Webpack, along with any other files
// present in this directory. You're encouraged to place your actual application logic in
// a relevant structure within app/javascript and only use these pack files to reference
// that code so it'll be compiled.
require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")
var jQuery = require("jquery")
// import jQuery from "jquery";
global.$ = global.jQuery = jQuery;
window.$ = window.jQuery = jQuery;
require('bootstrap');
require('admin-lte')
// Uncomment to copy all static images under ../images to the output folder and reference
// them with the image_pack_tag helper in views (e.g <%= image_pack_tag 'rails.png' %>)
// or the `imagePath` JavaScript helper below.
//
// const images = require.context('../images', true)
// const imagePath = (name) => images(name, true) 
```

默认情况下添加注释代码和`require`的前四行。这些是 Rails 在大多数应用程序中需要的库或包。新的行增加了 jQuery 和其他包或库可能期望存在的变量。最后，`bootstrap`和`admin-lte`需要对整个 Rails 应用程序可用。

## [T3 为什么是`require`而不是`import`？](#why-raw-require-endraw-and-not-raw-import-endraw-)

如果你最近做过 javascript 开发，你可能会问为什么我们使用`require`而不是`import`。这里的答案相当简单。基于我们正在使用和添加的包，他们希望其他人先加载。更具体地说，`admin-lte`希望全局变量`jQuery`在加载时存在。使用`require`可以保证代码按照文件的顺序执行，而`import`通常是经过预处理并无序执行的。

最后，我们需要在自动创建的应用程序样式表的底部添加一些导入。如果您在过去几年中做过 Rails 开发，您可能已经与资产管道进行过交互，这是 Rails 处理 javascript、样式表和任何其他静态资产的方式。

`app/assets/stylesheets/application.css`

```
*
 * This  is  a  manifest  file  that'll  be  compiled  into  application.css, which  will  include  all  the  files * listed  below.
 *
 * Any  CSS  and  SCSS  file  within  this  directory, lib/assets/stylesheets, or  any  plugin's * vendor/assets/stylesheets  directory  can  be  referenced  here  using  a  relative  path.
 *
 * You're  free  to  add  application-wide  styles  to  this  file  and  they'll  appear  at  the  bottom  of  the * compiled  file  so  the  styles  you  add  here  take  precedence  over  styles  defined  in  any  other  CSS/SCSS * files  in  this  directory. Styles  in  this  file  should  be  added  after  the  last  require_* statement.
 * It  is  generally  better  to  create  a  new  file  per  style  scope.
 *
 *= require_tree .
 *= require_self */
@import  url("https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,600,700&lang=en");  @import 'bootstrap';  @import 'admin-lte/dist/css/adminlte'; 
```

在 CSS 中，您可以通过使用`@import`语法来包含其他样式表或字体。在上面的样式表中，我们从 AdminLTE 的样式表中引用的 Google 字体中添加了一种字体。接下来，我们包括 Bootstrap、AdminLTE 和 Font Awesome(admin LTE 引用的图标)的样式表。

在下一节中，我们将添加一些控制器和模板，以添加必要的标记来构建应用程序。

在开始实现布局之前，我们需要应用一个路由和控制器来绕过默认的欢迎页面。我们还将在应用程序中使用这个控制器。

让我们停下来提一下，Rails 自带了内置的命令行生成器来生成控制器、模型和邮件。以及生成数据库迁移文件或甚至生成所有这些组合的支架。在我们的例子中，让我们关注控制器生成器。该生成器的通用格式为:

```
rails generate controller Activity mine feed 
```

它为我们在命令行中包含的操作创建了一个带有空方法的控制器。它创建了模板文件、测试文件和其他辅助文件。

让我们对路由文件进行一次快速编辑，以定义一个默认路由，将“root to: 'activity#mine '”添加到路由文件的底部，如下所示:

```
Rails.application.routes.draw do
  get ‘activity/mine’
  get ‘activity/feed’
  root to: ‘activity#mine’
end 
```

由于我们在本章前面已经设置了主题包、应用程序 javascript 文件和应用程序样式表文件，我们可以很快地开始编写实现这个主题所需的标记。

首先，我们可以修改主布局文件，以包含必要的元素和类来包装将由路由模板提供的内容。

`app/views/layouts/application.html.erb`

```
<!DOCTYPE html>
<html>
  <head>
    Some App
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
    <script src="https://kit.fontawesome.com/6d90f39943.js"></script> <-- Make your own FontAwesome Kit on their site -->
  </head>
<body class="sidebar-mini">
    <div class="wrapper">
      <%= render partial: "layouts/header" %>
      <%= render partial: "layouts/sidebar" %>
      <div class="content-wrapper">
        <%= yield %>
      </div>
      <%= render partial: "layouts/footer" %>
    </div>
  </body>
</html> 
```

这个文件的细节并不都与 Bootstrap 主题的添加相关，所以我们将只涉及那些重要的内容。首先，向 body 元素添加一个类，`body class=”sidebar-mini`。接下来，`<div class="wrapper">`和“

” are added as wrapping elements to the first the page and then the inner content. You may notice there is some included Ruby code in there as well. Those `render partial:` calls allow us to add markup from other template files, in that position. In this case, it adds various larger parts of the application template such as the header, sidebar or footer. Plus, it keeps the layout to only the most necessary foundational elements to build the page structure. Then, `<%= yield %>` which is the Rails’ template method for adding the template from the controller and action called.

因为我们使用的是分部模板，所以接下来我们继续创建头文件。

这里需要注意的一点是模板文件名中下划线的使用。在 Rails 中，部分模板文件在文件名的开头用下划线标出。什么是偏音？片段只是将模板分解成小块的一种方式。这种方法在诸如循环遍历一组项目的情况下发挥了更大的作用。在那里，您将使模板块显示项目信息的一部分。例如，标题模板的标题是`_header.erb`。

`app/views/layout/_header.erb`

```
<nav class="main-header navbar navbar-expand navbar-white navbar-light border-bottom">
    <!-- Left navbar links -->
    <ul class="navbar-nav">
      <li class="nav-item">
        <a class="nav-link" data-widget="pushmenu" href="#"><i class="fas fa-bars"></i></a>
      </li>
      <li class="nav-item d-none d-sm-inline-block">
        <a href="index3.html" class="nav-link">Home</a>
      </li>
      <li class="nav-item d-none d-sm-inline-block">
        <a href="#" class="nav-link">Contact</a>
      </li>
    </ul>
<!-- SEARCH FORM -->
    <form class="form-inline ml-3">
      <div class="input-group input-group-sm">
        <input class="form-control form-control-navbar" type="search" placeholder="Search" aria-label="Search">
        <div class="input-group-append">
          <button class="btn btn-navbar" type="submit">
            <i class="fas fa-search"></i>
          </button>
        </div>
      </div>
    </form>
<!-- Right navbar links -->
    <ul class="navbar-nav ml-auto">
      <!-- Messages Dropdown Menu -->
      <li class="nav-item dropdown">
        <a class="nav-link" data-toggle="dropdown" href="#">
          <i class="far fa-comments"></i>
          <span class="badge badge-danger navbar-badge">3</span>
        </a>
        <div class="dropdown-menu dropdown-menu-lg dropdown-menu-right">
          <a href="#" class="dropdown-item">
            <!-- Message Start -->
            <div class="media">
              <img src="http://placehold.it/128x128" alt="User Avatar" class="img-size-50 mr-3 img-circle">
              <div class="media-body">
                <h3 class="dropdown-item-title">
                  Brad Diesel
                  <span class="float-right text-sm text-danger"><i class="fas fa-star"></i></span>
                </h3>
                <p class="text-sm">Call me whenever you can...</p>
                <p class="text-sm text-muted"><i class="far fa-clock mr-1"></i> 4 Hours Ago</p>
              </div>
            </div>
            <!-- Message End -->
          </a>
          <div class="dropdown-divider"></div>
          <a href="#" class="dropdown-item">
            <!-- Message Start -->
            <div class="media">
              <img src="http://placehold.it/128x128" alt="User Avatar" class="img-size-50 img-circle mr-3">
              <div class="media-body">
                <h3 class="dropdown-item-title">
                  John Pierce
                  <span class="float-right text-sm text-muted"><i class="fas fa-star"></i></span>
                </h3>
                <p class="text-sm">I got your message bro</p>
                <p class="text-sm text-muted"><i class="far fa-clock mr-1"></i> 4 Hours Ago</p>
              </div>
            </div>
            <!-- Message End -->
          </a>
          <div class="dropdown-divider"></div>
          <a href="#" class="dropdown-item">
            <!-- Message Start -->
            <div class="media">
              <img src="http://placehold.it/128x128" alt="User Avatar" class="img-size-50 img-circle mr-3">
              <div class="media-body">
                <h3 class="dropdown-item-title">
                  Nora Silvester
                  <span class="float-right text-sm text-warning"><i class="fas fa-star"></i></span>
                </h3>
                <p class="text-sm">The subject goes here</p>
                <p class="text-sm text-muted"><i class="far fa-clock mr-1"></i> 4 Hours Ago</p>
              </div>
            </div>
            <!-- Message End -->
          </a>
          <div class="dropdown-divider"></div>
          <a href="#" class="dropdown-item dropdown-footer">See All Messages</a>
        </div>
      </li>
      <!-- Notifications Dropdown Menu -->
      <li class="nav-item dropdown">
        <a class="nav-link" data-toggle="dropdown" href="#">
          <i class="far fa-bell"></i>
          <span class="badge badge-warning navbar-badge">15</span>
        </a>
        <div class="dropdown-menu dropdown-menu-lg dropdown-menu-right">
          <span class="dropdown-item dropdown-header">15 Notifications</span>
          <div class="dropdown-divider"></div>
          <a href="#" class="dropdown-item">
            <i class="fas fa-envelope mr-2"></i> 4 new messages
            <span class="float-right text-muted text-sm">3 mins</span>
          </a>
          <div class="dropdown-divider"></div>
          <a href="#" class="dropdown-item">
            <i class="fas fa-users mr-2"></i> 8 friend requests
            <span class="float-right text-muted text-sm">12 hours</span>
          </a>
          <div class="dropdown-divider"></div>
          <a href="#" class="dropdown-item">
            <i class="fas fa-file mr-2"></i> 3 new reports
            <span class="float-right text-muted text-sm">2 days</span>
          </a>
          <div class="dropdown-divider"></div>
          <a href="#" class="dropdown-item dropdown-footer">See All Notifications</a>
        </div>
      </li>
      <li class="nav-item">
        <a class="nav-link" data-widget="control-sidebar" data-slide="true" href="#"><i class="fas fa-th-large"></i></a>
      </li>
    </ul>
  </nav> 
```

该模板包含应用程序标题/徽标、下拉列表和导航所需的任何附加链接的结构。为了设置应用程序模板结构，我们可以留下一些演示数据和元素来展示下拉菜单的外观和工作方式。

下一个要创建的分部是`_sidebar`模板:

`app/views/layouts/_sidebar.erb`

```
<aside class="main-sidebar sidebar-dark-primary elevation-4">
    <!-- Brand Logo -->
  <a href="index3.html" class="brand-link">
    <span class="brand-text font-weight-light">Standup App</span>
  </a>
<!-- Sidebar -->
  <div class="slimScrollDiv" style="position: relative; overflow: hidden; width: auto; height: 652px;"><div class="sidebar" style="min-height: 652px; overflow: hidden; width: auto; height: 652px;">
    <!-- Sidebar user panel (optional) -->
    <div class="user-panel mt-3 pb-3 mb-3 d-flex">
      <div class="image">
        <img src="http://placehold.it/160x160" class="img-circle elevation-2" alt="User Image">
      </div>
      <div class="info">
        <a href="#" class="d-block">User Name</a>
      </div>
    </div>
<!-- Sidebar Menu -->
    <nav class="mt-2">
      <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu" data-accordion="false">
        <!-- Add icons to the links using the .nav-icon class
             with font-awesome or any other icon font library -->
        <li class="nav-item has-treeview menu-open">
          <a href="#" class="nav-link active">
            <i class="nav-icon fas fa-tachometer-alt"></i>
            <p>
              Activity
              <i class="right fas fa-angle-left"></i>
            </p>
          </a>
          <ul class="nav nav-treeview">
            <li class="nav-item">
              <a href="./index.html" class="nav-link active">
                <i class="far fa-circle nav-icon"></i>
                <p>Mine</p>
              </a>
            </li>
            <li class="nav-item">
              <a href="./index2.html" class="nav-link">
                <i class="far fa-circle nav-icon"></i>
                <p>Feed</p>
              </a>
            </li>
          </ul>
        </li>
      </ul>
    </nav>
    <!-- /.sidebar-menu -->
  </div>
  <!-- /.sidebar -->
</aside>
As you see, the sidebar partial is a bit smaller and more straightforward. It contains a little bit of user information and links to other parts of the application that we change later.

The last major piece of the layout is the footer. It is simple, and it is short. Again, it is another partial. Here is the ERB markup:

app/views/layouts/_foooter.erb
<footer class="main-footer">
  <strong>Copyright © 2019 Your Stuff.</strong>
  All rights reserved.
</footer>
Now, to have all of this show up on a page, you can edit the root controller action’s template to use the theme’s div classes

app/views/activity/mine.html.erb
<div class="content-header">
  <div class="container-fluid">
    <div class="row mb-2">
      <div class="col-sm-6">
        <h1 class="m-0 text-dark">Some Page</h1>
      </div>
    </div>
  </div>
</div>
<section class="content">
  <div class="container-fluid">
    <div class="row">
      <div class="col-lg-12">
        <div class="card">
          <div class="card-body">
            <span>Stuff</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</section> 
```

这就是用 Bootstrap 4 和 Rails 6 设置的内容。