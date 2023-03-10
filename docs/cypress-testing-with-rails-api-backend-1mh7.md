# 用 Rails API 后端进行 Cypress 测试

> 原文：<https://dev.to/tomsfernandez/cypress-testing-with-rails-api-backend-1mh7>

在我们当前的一个项目中，我们开始用 Cypress 测试 React 前端的关键功能。当我们开始编写我们的测试时，我们偶然发现了一个问题，我们如何以一种安全的方式建立和清除在测试中创建/修改的数据呢？

我们不能直接在我们的 API 中做，因为我们的前端是由几个 API 提供的。我们还希望我们的测试不要用 Ruby 编写，所以不要选择 cypress-on-rails gem。

我们的主要目标是做到这一点**，而不必向我们的 Rails API** 添加不需要的功能。

我们的第一个方法是创建一个 Rails 控制器，它只能用于 Cypress 测试。我们选择通过*环境*变量和认证令牌来保护它。

这个选项有一个很大的优点:它非常容易创建和维护，它只是我们生产代码中的一个非常大的控制器。

可悲的是，有许多缺点:

*   作为测试助手的代码位于生产和业务代码的中间。
*   我们必须非常小心控制器的安全性。让一个控制者能够删除级联的几个我们的实体是一个巨大的风险。

最后一点对我们来说非常重要。API 部署中的一个错误可能会导致有人删除我们一半的数据库！。

这就是为什么我们考虑另一个选择:*使用 Rails 引擎*。

优点:

*   我们的主应用程序中的活动记录模型可以从引擎中引用。
*   Cypress 使用的端点在生产中不可用。
*   端点**的代码在产品包**中甚至不可用。

这确实降低了部署配置中的错误导致端点可用的几率。

## 代码

让我们首先创建一个新的 Rails 项目:

```
$ rails new cypress_api --api 
```

Enter fullscreen mode Exit fullscreen mode

在我们新创建的 API 中，让我们创建引擎来声明 cypress 特定的端点:

```
$ rails plugin new cypress --mountable 
```

Enter fullscreen mode Exit fullscreen mode

这将使用我们新的 Rails 引擎在根级别创建一个 cypress 文件夹，并将 gem 配置附加到我们的主 gem 文件中。

运行 *bundle install* 并从 engines gemspec 中删除 TODOs，以便能够启动应用程序。

现在，让我们在主应用程序中创建一个新的控制器来表示 API 是活动的。将此代码写入 app/controllers/status _ controller . Rb:

```
class StatusController < ActionController::API

  def status
    render json: {"status": "OK"}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们在引擎内部做同样的事情，但是使用不同的信息。cypress/app/controllers/cypress/status _ controller . Rb 内部:

```
module Cypress
  class StatusController < ActionController::API

    def status
      render json: {"status": "Cypress OK"}
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

另外，在 config/routes.rb 和 cypress/config/routes.rb 中添加相应的路由配置。

最后，我们必须将引擎的端点安装到我们的主应用程序上。问题是，我们不能静态地这样做，因为我们不希望端点在任何时候都可用。我们必须动态地做这件事。

为此，我们必须将这个代码片段添加到 cypress/lib/cypress/engine.rb 中的 Engines 主类:

```
 config.cypress_engine = ActiveSupport::OrderedOptions.new
  initializer 'cypress_engine.configuration' do |app|
    if app.config.cypress_engine[:mounted_path]
      app.routes.append do
        mount Cypress::Engine => app.config.cypress_engine[:mounted_path]
      end
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

那么这个片段是做什么的呢？如果在我们的环境文件或应用程序文件的任何地方声明了 *config.cypress_engine* 选项，引擎端点将被附加到主应用程序的前面。

现在，让我们在 development.rb 文件中声明这个选项。

```
 config.cypress_engine.mounted_path = "/cypress" 
```

Enter fullscreen mode Exit fullscreen mode

我们现在声明我们的 Cypress 引擎端点将被安装到我们的主应用程序中，前缀为 *cypress* 。

我们来试试吧！在开发模式下启动 Rails，进入 *"/status"* 和 *"cypress/status"* 。我们可以看到两个端点都可用！。

现在，让我们在生产模式下启动应用程序。如果我们尝试访问 *"/cypress/status"* ，我们可以看到返回了 404。

太好了！现在，Cypress 端点在生产环境中不可用，但仍然捆绑在我们的应用程序中。为了避免在生产过程中向我们的应用程序添加端点，我们可以将 Gem 限制为只在开发过程中安装。

```
 group :cypress do
    gem "cypress", path: "cypress"
  end 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以为我们的引擎增加另一个安全级别，以防有人错误地删除这个配置。

将这段代码添加到 cypress/lib/cypress/engine.rb 中我们之前添加的代码下面:

```
 initializer "cypress_engine.cypress_only" do
    unless Rails.env.development?
      abort <<-END.strip_heredoc Cypress Engine is activated in the #{Rails.env} environment. This is
        usually a mistake. To ensure it's only activated in cypress
        mode, move it to the development group of your Gemfile:

            gem 'cypress', group: :development END
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

如果 cypress 引擎用在开发以外的环境中，Rails 启动将被中止。

你可以在这个回购里找到所有的代码:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [汤姆斯费尔南德斯](https://github.com/tomsfernandez)/[Rails-Cypress-API-Example](https://github.com/tomsfernandez/Rails-Cypress-API-Example)

### 带有引擎的 Cypress Rails API 设置示例代码

[View on GitHub](https://github.com/tomsfernandez/Rails-Cypress-API-Example)

(在回购中，一个名为“cypress”的新环境的存在是为了增加另一个安全级别，但这并不是真正必要的)