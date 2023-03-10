# 向 Rails 应用程序添加特定于页面的 JS

> 原文：<https://dev.to/botreetech/adding-page-specific-js-to-rails-application-112i>

[![Adding Page Specific JS to Rails Application](img/d9aad4441f01ca283b17745caed7d8e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6UYaXgN7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.botreetechnologies.com/blog/wp-content/uploads/2019/06/Adding-Page-Specific-JS-to-Rails-Application.png)

在 [Rails 应用](https://www.botreetechnologies.com/ruby-on-rails-development)中，你可以在帕洛玛 [gem](https://www.botreetechnologies.com/blog/pagy-a-new-pagination-gem-in-the-realm-of-rails-world) 的帮助下非常容易地管理特定页面的 JS。Paloma gem 是管理 Rails JavaScript 文件的一种逻辑方式。

使用 Paloma gem 的要求

*   jQuery 1.7 或更高版本
*   Rails 3.1 或更高版本

Paloma 是一个非常漂亮的库，通过它你可以根据页面及其动作加载 JS。它的设置花费较少的时间，另一方面性能非常高。

## 帕洛玛的优点

*   您可以按照控制器来组织 JavaScript 文件，就像 Rails 的视图文件夹结构一样。
*   您可以为控制器的每个动作添加 JavaScript 文件。
*   您还可以选择希望每页运行的 JavaScript 代码及其操作。
*   [Ruby on Rails 开发](https://www.botreetechnologies.com/blog/ruby-on-rails-development-obstacles-opportunities)变量很容易在 JavaScript 文件中找到。

### 如何安装 Paloma

**1。将宝石添加到宝石文件**

不带捆绑器

*须藤宝石安装帕洛玛*

使用 bundler，将此添加到您的 gem 文件中

*宝石‘帕洛玛’*

**2。在你的 application.js 文件中需要 Paloma**

*//=要求 paloma*

### 阅读也- [让你的 JavaScript 提醒和确认框在 10 分钟内变得更有趣](https://www.botreetechnologies.com/blog/make-your-javascript-alerts-and-confirmation-boxes-more-fancier-in-10-minutes)

**3。将 Paloma 钩子添加到你的布局文件**