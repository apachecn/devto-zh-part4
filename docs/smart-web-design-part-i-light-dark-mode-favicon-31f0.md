# 智能网页设计。第一部分:亮/暗模式图标。

> 原文：<https://dev.to/rumkin/smart-web-design-part-i-light-dark-mode-favicon-31f0>

今天，我们有了新的超级酷的能力来检测操作系统的用户界面主题，并根据它改变网站视图。它使我们能够使用新的技术来编写主题化的、易于定制的 css 和 html。在这一系列的文章中，我将告诉你如何为你的 web 应用程序创建简单的主题化设计。

虽然我们可以用 CSS 和 HTML 来改变页面的可视化，但是有一个元素仍然没有这种能力。你已经知道我在说什么了。是的，这是一个图标。

如果你在黑暗模式下看一下 Dev.to 或 Github 的图标，你会发现它们几乎看不见了。我们需要改变它，使图标在主题切换时做出反应。最符合逻辑的方法是 link 元素的`media`属性，它允许 favicon 对传递给属性值的 CSS 媒体查询做出反应。但遗憾的是，link 的 media 属性支持的媒体查询列表不包括`prefers-color-scheme`。

幸运的是，我们可以使用 JavaScript 来实现它。好吧，让我们开始吧。

这是它如何工作的现场预览。

> **TL；对于那些想要现成解决方案的人，请使用图书馆:** 
> 
> ## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [拉姆金](https://github.com/rumkin) / [图标切换](https://github.com/rumkin/favicon-switcher)
> 
> ### 让 favicon 对媒体查询做出反应

## 监听主题切换

我们需要从页面头部收集所有链接元素，获取`media`属性，并使用`window.matchMedia()`方法匹配它。这个方法返回 [MediaQueryList](https://developer.mozilla.org/en-US/docs/Web/API/MediaQueryList) ，它允许监听更改，我们将使用它:

```
window.matchMedia('(prefers-color-scheme:light)').addListener((e) => {
  e.matches // Determine wether query matched or unmatched
}) 
```

## 添加图标

现在我们需要将不同主题的图标插入到页面主体:

```
 <link rel="icon" media="(prefers-color-scheme:dark)" href="favicon-dark.png" type="image/png" />
  <link rel="icon" media="(prefers-color-scheme:light)" href="favicon-light.png" type="image/png" /> 
```

## 切换图标

要让浏览器切换标签的图标，让`<link>`元素成为`<head>`中的最后一个`<link>`元素就足够了。这很好，但 Chrome 目前有一个错误，在某些情况下会破坏这种图标切换。为了避免这个 bug，我们需要创建新的`<link>`，并将其添加到其他链接之后的 head children 列表中。

```
const favicon = document.createElement('link')
link.setAttribute('rel', 'favicon icon')
head.appendChild(link)

// Listen media change
window.matchMedia('(prefers-color-scheme:light)')
.addListener((e) => {
  if (! e.matches) {
    return
  }
  // Apply new favicon source
  const source = document.querySelector('link[rel*="icon",media="(prefers-color-scheme:light)"]')

  if (source === null) {
    return
  }

  link.setAttribute('type', source.type)
  link.setAttribute('href', source.href)
}) 
```

只需复制最后一个表达式，将`light`替换为`dark`即可启用黑暗主题图标。

> 注意！由于可能的 DOM 突变，我们检查 source 是否是`null`。

## 结论

现在你知道如何制作一个网页图标来对主题切换做出反应。

* * *

感谢阅读。使用 [favicon-switcher](https://github.com/rumkin/favicon-switcher) ，覆盖更多用例，支持其他媒体查询，如`max-width`、`min-width`等。

## 学分

Linda Xu 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片