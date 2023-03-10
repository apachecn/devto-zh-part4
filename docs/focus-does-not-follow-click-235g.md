# 焦点不跟随单击()

> 原文：<https://dev.to/michalbryxi/focus-does-not-follow-click-235g>

当你在网页上点击时,*当前聚焦的元素*相应地改变。如果你想看看哪个 DOM 元素现在被聚焦，你可以简单地*点击某个元素上的*，打开开发工具并执行:

```
document.activeElement

> <textarea class="articleform__body" id="article_body_markdown" placeholder="Body Markdown" name="body_markdown" style="height: 714px;"></textarea> 
```

我假设对于*JavaScript*[click()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/click)调用也是如此。但是经过太长时间绝望的代码调试，我发现:

> 焦点不跟随单击()

因此，如果你试图在你的 JS 控制台中直接在 dev.to 上运行以下代码:

```
document.querySelector("#nav-search").click();
console.log("Active element:", document.activeElement);

> Active element: <textarea class="articleform__body" id="article_body_markdown" placeholder="Body Markdown" name="body_markdown" style="height: 798px;"></textarea> 
```

活动元素不会是`click-ed`搜索栏。为此，您必须运行:

```
document.querySelector("#nav-search").focus();
console.log("Active element:", document.activeElement);

> Active element: <input class="nav-search-form__input" type="text" name="q" id="nav-search" placeholder="search" autocomplete="off" aria-label="search"> 
```

你可以玩一个[更完整的演示](https://codesandbox.io/s/click-vs-focus-bgjtl)，包括`focusout()`如何融入等式。

吸取教训。

* * *

来自[的封面照片](https://pixabay.com/users/free-photos-242387/)在 [pixabay 上](https://pixabay.com/photos/office-keyboard-job-work-381228/)