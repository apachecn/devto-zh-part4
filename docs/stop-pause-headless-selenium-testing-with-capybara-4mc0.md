# 用水豚停止/暂停无头硒测试

> 原文：<https://dev.to/n350071/stop-pause-headless-selenium-testing-with-capybara-4mc0>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 🤔情况

当你想用真实显示进行调试时。

设定是这样的。

```
#spec/rails_helper.rb
Capybara.register_driver :headless_chrome do |app|
  Capybara::Selenium::Driver.new(
    app,
    browser: :chrome,
    options: browser_options,
    http_client: Selenium::WebDriver::Remote::Http::Default.new
  )
end
browser_options = ::Selenium::WebDriver::Chrome::Options.new.tap do |opts|
  opts.args << '--headless'
end 
```

## 👍解决办法

注释掉`opts.args << '--headless'`行。

```
#spec/rails_helper.rb
Capybara.register_driver :headless_chrome do |app|
  Capybara::Selenium::Driver.new(
    app,
    browser: :chrome,
    options: browser_options,
    http_client: Selenium::WebDriver::Remote::Http::Default.new
  )
end
browser_options = ::Selenium::WebDriver::Chrome::Options.new.tap do |opts|
#  opts.args << '--headless'
end 
```

## 📚参考

*   [在 Rails 中导入 Capybara + Selenium，在 Chrome Headless 模式下执行的步骤- Qiita](https://qiita.com/zo3_0005/items/a5dcede868a3d1cca4a8)