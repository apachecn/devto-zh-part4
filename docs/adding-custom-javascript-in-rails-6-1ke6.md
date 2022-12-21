# 在 Rails 6 中添加自定义 JavaScript

> 原文：<https://dev.to/morinoko/adding-custom-javascript-in-rails-6-1ke6>

在 Rails 6 中，JavaScript 资产不再包含在`app/assets/`目录中，而是被转移到由 Webpacker 处理的单独目录`app/javascript`中。这很好，但是当我想添加一些我自己的定制 javascript 时，没有任何清晰的文档描述应该如何做。

经过一些实验和在互联网上的挖掘，这里有几个方法似乎是有效的。请注意，我绝不是 javascript 专家，所以如果有更好的方法——或者我遗漏了其他方法——请在评论中告诉我！

# 方法一:创建自定义目录，并在`application.js`中要求

如果你看一下`application.js`，顶部的注释建议你“将实际的应用程序逻辑放在 app/javascript 的相关结构中，并且只使用这些包文件来引用代码，这样它就会被编译。”

```
// app/javascript/packs/application.js

// This file is automatically compiled by Webpack, along with any other files
// present in this directory. You're encouraged to place your actual application logic in
// a relevant structure within app/javascript and only use these pack files to reference
// that code so it'll be compiled.

require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels") 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过在`app/javascript/`中添加一个自定义目录来进行设置，例如`custom/`，然后在`application.js`中需要其中的文件。

例如，如果您在`app/javascript/custom/`目录中有一个名为`home.js`的 javascript 文件，您可以用`require()` :
加载它

```
// app/javascript/packs/application.js

// ...

require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")

require("custom/home") 
```

Enter fullscreen mode Exit fullscreen mode

因为在`application.js`中需要它，所以定制的 javascript 将和所有其他 javascript 一起编译成一个带有时间戳的`application.js`文件，看起来有点像`application-a03d1868c0a3f825e53e.js`。

这是由`app/views/layouts/application.html.erb`中的`<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>`标签加载的，在您创建新的 Rails 应用程序时自动创建。

为了看起来更清楚，您还可以将 javascript 文件命名为`index.js`，并要求它带有一个简单的目录引用，如`require("custom")`，因为当给定一个目录的路径时，`require()`会查找一个`index.js`文件(如果没有`index.js`，它将失败)。

# 方法二:在`app/javascript/packs`目录下添加自定义的 JavaScipt

如果出于某种原因，您不想创建另一个目录，您可以选择在`app/javascript/packs`目录中添加定制的 javascript 文件。然后，用`require()`要求`application.js`中的文件。

例如，如果在`app/javascript/packs`中有一个名为`custom.js`的文件，如下所示，它将与所有其他 javascript:
一起被编译成带时间戳的`application.js`文件

```
// app/javascript/packs/application.js

require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")

require("packs/custom") 
```

Enter fullscreen mode Exit fullscreen mode

# 方法三:使用`javascript_pack_tag`进行单独的 JavaScript 文件(包)

如果您不希望您的定制 javascript 和其他东西一起被编译到`application.js`中，您可以让 Rails 将它编译到一个单独的文件中，或者“打包”

为此，在`app/javascript/packs`中添加一个自定义文件，例如`custom.js`，然后在视图中需要该文件的地方使用`javascript_pack_tag`助手，如下所示:`<%= javascript_pack_tag 'custom' %>`

定制的 javascript 将与 javascript 的其余部分分开*编译*成一个带时间戳的`custom.js`，看起来像这样:`custom-a03d1756c0a3f825e53e.js`

如果您在`layouts/views/application.html.erb` :
中的结尾`body`标签之前添加了定制的 javascript，看起来会是这样

```
# app/layouts/views/application.html.erb

<!DOCTYPE html>
<html>
  <head>
    My App
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body class="<%= controller_name %>  <%= action_name %>">
    <%= render 'layouts/header' %>

    <%= yield %>

    <%= render 'layouts/footer' %>

    <%= javascript_pack_tag 'custom' %>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！