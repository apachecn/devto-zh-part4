# 测试设置 rails 5.2

> 原文：<https://dev.to/tallerguy/test-setup-rails-5-2-i5m>

# 水豚，硒与定计

我观察这三个已经有一段时间了。主要是在旧版本的 rails 中，以及作为这些 gems 旧版本的后续效果。

开始用 rails 5.2 做一个爱好项目(为什么不是 rails 6？).这是我必须要做的事情。

首先，水豚和硒来预装。

你还需要什么:

*   水豚-截图:在这里找到详情
*   database_cleaner:在这里找到详细信息

水豚截图每次你的测试出现错误都会截图，会在你挠(拔？)你的头(头发？).

database_cleaner 用于清理测试数据库，以一个干净的数据库开始每个测试。

好，我们继续，你在哪里添加设置，设置是什么

*假设*:测试框架是 RSpec

## 水豚

在 spec helper(rspec/spec _ helper . Rb)中，在顶部添加以下内容

`require 'capybara/rspec'`

## 水豚截图

在 spec helper(r spec/spec _ helper . Rb)中，在要求水豚的下方添加以下内容

`require 'capybara-screenshot/rspec'`

## 硒

在 spec helper(rspec/spec _ helper . Rb)中，在 capybara-screeshot
`require 'selenium-webdriver'`下面添加以下内容

## 数据库清理器

在 rspec/support 中添加一个名为 database_cleaner.rb 的文件。

```
RSpec.configure do |config|

  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
  end

  config.before(:each) do
    DatabaseCleaner.strategy = :truncation
  end

  config.before(:each, js: true) do
    DatabaseCleaner.strategy = :truncation
  end

  config.before(:each) do
    DatabaseCleaner.start
  end

  config.after(:each) do
    DatabaseCleaner.clean
  end
end 
```

特别注意

```
config.before(:each) do
  DatabaseCleaner.strategy = :truncation
end 
```

当我们使用 selenium 和 devise 通过访问无头 chrome 中的登录页面进行登录时，这个设置就变得很重要了。

好了，现在是让 selenium 与 capybara 一起工作的主要设置，在 require 语句之后添加以下内容。

```
Capybara.default_driver = :selenium

options = Selenium::WebDriver::Chrome::Options.new(
  args: %w[headless disable-gpu no-sandbox disable-dev-shm-usage window-size=1600,1200 verbose]
)

Capybara.register_driver :selenium do |app|
  Capybara::Selenium::Driver.new(app, browser: :chrome, options: options)
end

Capybara.javascript_driver = :selenium 
```

现在一个接一个，

*   我们将在无头浏览器上运行测试的默认驱动程序设置为 selenium。还有其他的，像[恶作剧鬼](https://github.com/teampoltergeist/poltergeist)
*   我们为运行测试套件的浏览器选择的选项， *headless* 部分很重要，你不希望每次测试都弹出浏览器。
*   将 javascript_driver 设置为 selenium，让 capybara 注意 ajax 和 async 的优点)。

现在所有的设置都完成了，当您实际编写一个登录测试时，它将如何运行呢？

为了让所有这些工作，在 rspec/rails_helper.rb 中有一个设置

关掉它。Selenium 不知道事务中发生了什么(这里的细节[这里的](https://stackoverflow.com/questions/12326096/capybara-selenium-fault-and-redirect-example-com-when-without-everything-is-gre/12330557#12330557)和[这里的](https://stackoverflow.com/questions/6154687/rails-integration-test-with-selenium-as-webdriver-cant-sign-in)),因此，您得到的只是无效登录的错误。

记住**注意数据库设置中的这个**部分，如果你不把它截断，这一切都不会起作用。

这是为什么呢？还没有深入研究到可以写这方面的内容，但是期待很快会有一篇文章。

好了，现在就这样。以后再说。

继续编码。