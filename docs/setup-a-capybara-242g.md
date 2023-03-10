# 建立一个水豚

> 原文：<https://dev.to/n350071/setup-a-capybara-242g>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 1。安装 gem

```
# Gemfile
group :test do
  gem 'capybara'
  gem 'capybara-screenshot' # recommend
  gem 'selenium-webdriver'
end 
```

## 2。配置水豚

```
# spec_helper.rb
require 'capybara/rails' # For Rails app
require 'capybara/rspec' # Load RSpec 3.5+ support

# set up driver
Capybara.register_driver :headless_chrome do |app|
  Capybara::Selenium::Driver.new(
    app,
    browser: :chrome,
    http_client: client
    options: options)
end

## set up client
require 'selenium-webdriver'
client = Selenium::WebDriver.for :chrome # see also following link.

## set up options
options = Selenium::WebDriver::Chrome::Options.new.tap do |opts|
  opts.args << '--headless'
end

# configure
Capybara.configure do |config|
  config.ignore_hidden_elements = true
  config.default_max_wait_time = 3 #sec
end 
```

*   📚[水豚配置](https://www.rubydoc.info/github/jnicklas/capybara/Capybara#configure-class_method)
*   📚 [selenium 用户手册](https://docs.seleniumhq.org/docs/)
*   📚[硒新手册](https://seleniumhq.github.io/docs/site/en/)
*   📚[硒 api](https://seleniumhq.github.io/selenium/docs/api/rb/Selenium/WebDriver.html)