# CSS 选择器定位器

> 原文：<https://dev.to/tonetheman/css-selector-locators-7g9>

在 Selenium 中查找网页元素的另一种方法是使用 CSS 选择器。

同样，ID 或 Name 通常是在 Selenium 中定位元素的最佳选择，但是 CSS 选择器也很强大。

CSS 选择器也可以使用 ID 属性。我将在下面举例说明。因此，如果你有一些前端开发并了解 CSS 选择器，那么使用 CSS 选择器作为 Selenium 定位器将是一个不错的选择。

如果你不太了解 CSS 选择器，你可以使用 Chrome devtools 来轻松找到它们。

去检查并找到你想要的元素。右键单击元素并选择复制...然后选择选择器。

下面是 CSS 选择器在 Python Selenium 脚本中的样子。

```
 selector_path = "#form > main > div:nth-child(2) > section > section > section.hero-lockup > div > section > span.triptych-item.screenshots > div.triptych-item-meta > p > strong > span"
        e = driver.find_element(By.CSS_SELECTOR, selector_path) 
```

Enter fullscreen mode Exit fullscreen mode

有时 CSS 选择器实际上就是 ID。作为一个例子，dev.to 按钮“写一篇文章”CSS 选择器如下所示。

```
#write-link 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 CSS 选择器并且 DOM 结构发生变化，那么您的定位器可能会失败，结果您的测试也会失败。您应该尽可能选择最具体的 CSS 选择器(又名 ID 或 name)来帮助缓解这个问题。

MDN 关于 CSS 选择器的一个很好的参考可以在这里找到:[https://developer . Mozilla . org/en-US/docs/Learn/CSS/Introduction _ to _ CSS/Selectors](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Selectors)