# LWC–复制到剪贴板按钮

> 原文：<https://dev.to/brettmn/lwc-copy-to-clipboard-button-49pj>

[https://www.youtube.com/embed/Bn-K7BXlHQU](https://www.youtube.com/embed/Bn-K7BXlHQU)

嗨，我是布雷特和 WIPDeveloper.com。前几天我看到一个关于如何在 lightning web 组件中创建一个复制到剪贴板按钮的问题。所以我尝试了一下。这是我想到的。

我已经创建了一个组件，它标记了标题为“复制到剪贴板”的`lightning-card`，有两个文本字段，一个用于复制，一个用于粘贴，这样你就可以看到它，还有一个应该复制文本的按钮。

标记非常简单，有一个用于粘贴的复制输入，然后有一个调用复制到剪贴板的按钮。

在 JavaScript 中。复制到剪贴板的一个方法目前不做任何事情。因此，我们可以点击它，然后粘贴它，什么也不会发生。我的意思是，它现在正在粘贴空格，但我们想要做的是复制这个输入的内容。我们首先要做的是获取输入。所以我们必须找到那个东西

`copy-me`类别为`copy-me`的文本。

因此，针对`template.querySelector`进行查询，这将是`.copy-me`。

现在我们有了对输入的引用。我们将选择它。所以`select`应该突出显示文本

让我们试一下，保存它，当我们刷新页面并点击按钮`Copy Me!`时会发生什么。

我确实看到有人提到这可能在某些浏览器上不起作用。为了说明这一点，我们也将使用`setSelectionRange`。我们想从头到尾都是第一个角色。因此

我们只是在创造一些高数字。

现在我们不必担心，如果实现或使用它的浏览器不遵循 select 选项。

现在我们想使用`Copy`命令。我们使用谁呢`document.execCommand`然后是我们想要的命令类型的字符串，在这个例子中是`copy`。因此

`document.execCommand`。

收到。

保存这个

格式化它。

那就省省吧。

现在，当我们刷新页面并按下“按下复制”按钮时，它应该会突出显示，现在我们应该能够将“复制我”粘贴到第二个文本框中。这很有效。

我们可以把它换成其他的。按复制，然后粘贴到这里。我们可以把它粘贴进去，我们可以在这里做任何文字，复制文字，粘贴，粘贴。

走吧。就是这样。

如果你想复制，使用复制到剪贴板按钮和 lightning web 组件将是一种方法。

## 链接

*   [与剪贴板互动](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Interact_with_the_clipboard)
*   [htmlinputelement . select()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/select)
*   [htmlinputelement . setselectionrange()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/setSelectionRange)

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[WIPDeveloper.com](https://wipdeveloper.com/lwc-copy-to-clipboard-button/)上最先出现【LWC-复制到剪贴板】按钮。