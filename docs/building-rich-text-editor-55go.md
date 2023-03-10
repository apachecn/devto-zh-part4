# 构建富文本编辑器

> 原文：<https://dev.to/rafi993/building-rich-text-editor-55go>

富文本编辑器是你在几乎每个应用程序中都可以看到的东西之一，但通常它们的工作方式被一些库从大多数开发人员那里抽象出来，如 [DraftJs](https://github.com/facebook/draft-js) 、 [Trix](https://github.com/basecamp/trix) 或坚持使用传统的 **contentEditable** (稍后将详细介绍)。但是尝试从零开始构建它，只是为了看看它在这些编辑器中是如何工作的，而不是将它们视为魔术，这是很好的。

### 内容可编辑

contentEditable 是大多数浏览器都支持的一个属性，允许您使任何元素可编辑。打开开发工具，运行下面的代码片段

```
document.getElementsByTagName("body")[0].contentEditable = true 
```

现在整个页面应该可以编辑了，甚至像 **ctrl+b** 、 **ctrl+i** 、 **ctrl+u** 这样的键绑定也应该可以工作了。现在，您可能认为已经完成了富文本编辑器的构建。内容可编辑的真正问题来了，它并不是在所有的浏览器中都以同样的方式工作。因此，当你在 Chrome 中按 enter 键时，它会添加新的 **BR** 标签，而在 Firefox 中，每个标签会添加一个段落。更糟糕的是，当你从其他网站粘贴一些文本时，这种风格会被保留，并可能导致不同浏览器中不同的 DOM 元素。当您决定构建一个移动应用程序时，它无法呈现编辑器生成的 DOM，除非您使用 Webview，这可能会降低性能。

### 内容可编辑的好部分

contentEditable 提供了一种轻松呈现富文本的方法，因为它只是 DOM，你可以在其中呈现自定义的 React 组件(比如提示语)。粘贴确实有效(尽管这会导致不同的 DOM)。许多浏览器都支持它。

我们可以通过简单地在 Javascript 中维护我们自己的数据模型来利用 contentEditable 的优点而忽略它的缺点。将渲染保持为内容可编辑。像 **DraftJs** 这样的编辑器在内部做这件事。

### 尝试 1

让我们用降价格式来模拟我们的数据。首先，我们将构建一个简单的组件，它有一个内容可编辑的 div，并且它将有一个跟踪当前文本和光标位置的本地状态。我们可以在 contentEditable 上使用 **onKeyDown** ，就像你在 React 中对任何文本输入所做的那样。我们可以根据当前光标位置和输入的文本来处理退格键、删除键和箭头键。我们还应该检查所按的键是否是可打印的字符，如果不是就忽略它(alt，shift，capslock...).这里的代码是[分支**中的**](https://github.com/Rafi993/rich-text-editor)尝试-一次。现在我们的基本文本编辑器出来了，接下来我们需要处理粘贴。

### 处理粘贴

为了处理粘贴，我们将 **onPaste** 属性传递给 contentEditable div，并使用**e . prevent default()；**防止默认粘贴。我们可以使用
得到要粘贴的文本的纯文本省略样式

```
 e.clipboardData.getData("text/plain"); 
```

然后我们可以使用
手动粘贴它

```
 document.execCommand('insertHTML', false, newText); 
```

我们还更新了包含文本和光标位置的本地状态。在下一节中，我们将处理基本的富文本特性(粗体、斜体、下划线...).此外，稍后我们将构建一些功能，如在其中呈现 React 组件。

这个帖子其实是从
[交叉发布的 https://rafi993.me/posts/2019-28-07-建筑-丰富-文字-编辑-部分-1/](https://rafi993.me/posts/2019-28-07--Building-rich-text-editor-part-1/)