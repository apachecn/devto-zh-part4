# 避免水豚测试中的陈旧元素

> 原文：<https://dev.to/vier31/avoiding-stale-elements-in-capybara-tests-41p1>

昨天晚上，我遇到了一个新的错误:`StaleElementReferenceError`。

这是什么？这是 Selenium WebDriver 抛出的一个错误，我在 Ruby 的水豚测试中使用了这个错误。

当对所选元素的引用发生变化时，将引发该异常。

在我的例子中，我正在测试一个上传功能，并在上传完成后查看预览图像。

我在编写测试时犯的错误是，我声明了一个变量，在那里我存储了所选择的元素。

```
Then('(I )see the preview of {string}') do |name|
  preview_image = page.find(".preview-image")
  expect(preview_image[:style]).to include(name)
end 
```

Enter fullscreen mode Exit fullscreen mode

图像出现后，React 更改了 DOM，并在图像中加入了一个新元素。这导致变量内部的引用变得陈旧。因此，在一次重试中，水豚抛出了错误。

这种情况可以很容易地避免，通过编写不带变量的测试，如下所示:

```
Then('(I )see the preview of {string}') do |name|
  expect(page.find(".preview-image")[:style]).to include(name)
end 
```

Enter fullscreen mode Exit fullscreen mode

这样，水豚每次重试时都会检查元素。