# Random TIL #1

> 原文：<https://dev.to/amree/random-til-1-2g19>

我想我应该分享一些随机的，直到我得到我的工作票。让我们看看我还能坚持多久😆

## 相同单选按钮的名称

我偶然发现了这个问题，单选按钮没有被选中，即使它应该基于 URL 中给定的参数。

显然，问题是因为有另一个同名的单选按钮，所以这是被检查的那个。我们有两组单选按钮(桌面和移动)，它们都使用相同的名称。简单地在其中一个集合的名称前加上前缀就可以解决这个问题

## CircleCI 神器为水豚截图

事实证明，访问那些水豚截图非常容易。只需在本地配置中指定截图所在的目录，就能确保它出现在 CircleCI
的工件选项卡中

```
version: 2
jobs:
  build:
    docker:
      ...
    steps:
      ...
      ...
      - store_artifacts:
          path: /home/circleci/project/tmp/capybara 
```

Enter fullscreen mode Exit fullscreen mode

## 在移动视图中测试功能规格

为了测试一些只在手机上可用的用户界面，我们需要首先调整窗口的大小，这可以通过下面的代码来实现:

```
# spec/support/capybara_test_helper.rb
module CapybaraTestHelper
  def resize_to_mobile(width: 375, height: 667)
    page.current_window.resize_to(width, height)
  end
end

# spec/rails_helper.rb
RSpec.configure do |config|
  # ...
  config.include CapybaraTestHelper, type: :feature
end 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但是很明显，Capbybara 没有把窗口的大小调整回原来的大小，所以，你的眼镜可能会失效。为了确保它能被重置，请再次将这段代码放入您的 rails 助手:

```
RSpec.configure do |config|
  config.after(js: true, type: :feature) do
    page.current_window.resize_to(1600, 1024)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我以前使用过`.maximize`，它在本地工作正常，但是在 CircleCI 失败了，可能是因为文档中提到的不支持的驱动程序。所以，明确设置宽度和高度会有所帮助。

## 水豚亲体选择器

在我的一个规范中，我需要获取文本值，这是我可以定位的元素的第三个兄弟。因为它是唯一的文本，所以我可以使用父选择器并调用`.text`来获取值。使用`.first(:xpath, '..')`帮助:

```
find(
  'input[name="meh"]:checked',
  visible: false
).first(:xpath, '..').text 
```

Enter fullscreen mode Exit fullscreen mode

就这样了，伙计们。希望这些笔记能对未来的✌️有所帮助