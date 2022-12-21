# CSS 平滑滚动行为

> 原文：<https://dev.to/coryrylan/css-smooth-scroll-behavior-28op>

最近我想在我的一个网站上添加一个平滑滚动的效果，于是我去寻找一个 JavaScript 插件来实现它。令我惊讶的是，一个更新的 CSS 滚动行为 API 在许多浏览器中都可用！使用 CSS 滚动行为 API，我们可以链接到 HTML 页面的内部，并让浏览器滚动到页面的该部分，而不是立即跳转到该部分。我觉得这很酷，所以我想写一个演示它是如何工作的。

在过去，如果我们想要平滑地滚动到页面的某个部分，我们必须使用 JavaScript 来实现。现在有了 CSS 滚动行为，我们只用一行 CSS 就可以做到。使用链接，我可以链接到我的 HTML 页面的内部。

```
<a href="#some-content">jump to some content</a>

...

<p id="some-content">
  some content
</p> 
```

Enter fullscreen mode Exit fullscreen mode

有了这个小小的 HTML，用户可以点击链接，让页面跳转到下面的`some-content`段落。这种跳跃效果可能是一种有用的快捷方式，但突然移动到页面的一个完全不同的部分也会造成不协调。为了获得更好的体验，我们可以添加下面的 CSS:

```
html {
  scroll-behavior: smooth;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们点击链接时，浏览器将滚动到内容，而不是立即跳转到页面的这一部分。因为平滑滚动行为是通过 CSS 应用的，所以我们可以用 JavaScript 操纵滚动位置，并且仍然可以获得同样好的平滑滚动效果。

```
const button = document.querySelector('button');
button.addEventListener('click', () => window.scrollTo(0, 1400)); 
```

Enter fullscreen mode Exit fullscreen mode

[浏览器对 CSS 滚动行为的支持](https://caniuse.com/#search=scroll-behavior)非常好，因为它可以在下一版本的 Edge、Chrome 和 Firefox 中工作。你也可以检查一个完整的工作[演示](https://stackblitz.com/edit/js-diczif)