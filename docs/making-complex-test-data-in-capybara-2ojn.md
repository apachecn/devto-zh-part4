# 在水豚中制作复杂的测试数据

> 原文：<https://dev.to/n350071/making-complex-test-data-in-capybara-2ojn>

## 🦄首先，笔记

这项技术实际上不仅适用于水豚测试，也适用于 RSpec 测试。然而，主要的使用案例是水豚。

## 🤔情况

您需要为多个测试准备复杂的测试数据。

对于 Factorybot 来说似乎太复杂了。但是，UI 操作似乎很容易。

## 👍解决办法

你可以使用一个模块并包含它，在{}块之前调用它。

```
#rails_helper.rb
Dir[Rails.root.join('spec/rails_helpers/**/*.rb')].each { |f| require f } 
```

```
# spec/rails_helpers/book_operation_scripts.rb
module BookOperationScripts
  def create_published_book(author, options = {})
    click_on('publish a new book')
    fill_in 'v2_book[author]', with: options.fetch(:name, 'n350071')
    fill_in 'v2_book[title]', with: options.fetch(:name, 'enjoy ruby')
    fill_in 'v2_book[price]', with: '8'
    # ...
    page.execute_script("$('form#publish-form').submit()")
  end
end 
```

```
# spec/v2/feature/books_spec.rb
require 'rails_helper'

feature 'book/', js: true do
  include BookOperationScripts
  let(:author){ create(:v2_author) }
  before do
    create_v2_published_book(author)
  end
end 
```

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)