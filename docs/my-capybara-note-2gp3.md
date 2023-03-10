# 我的水豚笔记

> 原文：<https://dev.to/n350071/my-capybara-note-2gp3>

# 概述

水豚是很好的铁路 E2E(功能)测试。它模拟用户在浏览器上的行为，比如找到一个按钮并点击它，等待下一页。可以选择 Selenium Driver 或者其他作为 web 驱动。

# 设置

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 建立一个水豚

### n350071🇯🇵9 月 19 日 1911 分钟阅读

#rails](/n350071/setup-a-capybara-242g)[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 用水豚停止/暂停无头硒测试

### n350071🇯🇵9 月 10 日 191 分钟阅读

#capybara #rspec #rails](/n350071/stop-pause-headless-selenium-testing-with-capybara-4mc0)

# 用法

## 🔨准备

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 在水豚中制作复杂的测试数据

### n350071🇯🇵9 月 19 日 1911 分钟阅读

#rails](/n350071/making-complex-test-data-in-capybara-2ojn)

## 👁环顾你的网站

### HTML 内部

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 在水豚中发现

### n350071🇯🇵9 月 24 日 192 分钟阅读

#rails](/n350071/find-in-capybara-4ohm)[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 点击水豚中的链接

### n350071🇯🇵9 月 18 日 1911 分钟阅读

#rails](/n350071/link-in-capybara-336k)[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 在 Capybara Rails 测试中使用“在”缩小范围

### n350071🇯🇵9 月 18 日 1911 分钟阅读

#rails](/n350071/narrow-down-scope-with-within-in-capybara-rails-testing-14hi)

### 浏览器操作

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 水豚::会话页面导航

### n350071🇯🇵10 月 24 日 191 分钟阅读

#rails](/n350071/capybara-session-page-navigations-12de)

## 断言

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## capybara find.text 方法忽略换行符(转换为空格)

### n350071🇯🇵9 月 10 日 191 分钟阅读

#capybara #rspec #rails](/n350071/is-ignored-converted-to-a-space-at-capybara-find-text-3ef7)
[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 在水豚中测试 css(图标)

### n350071🇯🇵9 月 10 日 191 分钟阅读

#capybara #rspec #rails](/n350071/test-css-icon-with-capybara-5fb8)
[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## have_selector 适合水豚等待测试

### n350071🇯🇵9 月 18 日 1911 分钟阅读

#capybara #rspec #rails](/n350071/haveselector-is-good-for-waiting-test-in-capybara-5g5d)
[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 【水豚】喜欢分享 _ 示例

### n350071🇯🇵10 月 8 日 192 分钟阅读

#rails](/n350071/capybara-like-sharedexamples-18be)

```
# get current path
current_path

# spec/rails_helpers/feature_helper.rb
module FeatureHelpers
  def current_path_with_params
    uri = URI.parse(current_url)
    "#{uri.path}?#{uri.query}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 📝形式

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 水豚中的复选框

### n350071🇯🇵9 月 18 日 1911 分钟阅读

#rails](/n350071/checkbox-in-capybara-3emj)[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 在水豚中附加一个文件

### n350071🇯🇵9 月 19 日 1911 分钟阅读

#rails](/n350071/attach-a-file-in-capybara-52ke)[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 【水豚】寻找(‘输入’)。集合意志还没有完成！

### n350071🇯🇵12 月 17 日 191 分钟阅读

#capybara #rspec #rails](/n350071/capybara-find-input-set-will-have-not-done-5cpg)

```
# input type=text
fill_in 'nav-search', with: 'n350071 capybara'

# select box
select 'Most Views', from: 'dashhboard_sort'

# submit
page.execute_script("$('form#your-form').submit()") 
```

Enter fullscreen mode Exit fullscreen mode

## 🧙‍♂️操纵

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 运行 Javascript by Capybara 功能测试

### n350071🇯🇵9 月 10 日 191 分钟阅读

#capybara #rspec #rails](/n350071/run-javascript-by-capybara-feature-test-ob8)

```
# Change CSS/Style in Capybara
page.execute_script("$('selector').css('display','block')"); 
```

Enter fullscreen mode Exit fullscreen mode

## 🎁意见

*   我们应该使用用户将看到的内容，而不是 HTML 的源代码。
*   HTML 是测试的一个重要因素。
    *   我们应该对齐 HTML 标签，CSS 进行测试。如果它不同于一个相似的页面，测试就很困难。
    *   我们应该在 HTML 中使用正确的名称来支持简单的测试。
*   使用`find('label[for="model_attributes"]'`比 XPath 更好，因为这种方式是模拟用户的方式。