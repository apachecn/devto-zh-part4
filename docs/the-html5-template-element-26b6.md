# HTML5 模板元素。

> 原文：<https://dev.to/ahferroin7/the-html5-template-element-26b6>

模板化的内容在网络上是巨大的。大多数网站至少在服务器端或客户端进行某种形式的模板化，如果您正在编写单页应用程序(并且希望保持理智)，这在客户端是必不可少的。

然而，在使用 Web 组件的站点之外，客户端的任何模板化几乎总是使用专用的模板库或支持模板化的应用程序框架来完成。

然而，还有另一种方法:HTML5 `<template>`元素。

## 到底是什么`<template>`元素？

简而言之，它提供了一种简单的方法来定义一个可重用的 HTML 片段，可以像处理文档本身的内容一样对其进行操作，但没有实际更新 DOM 或编译和解析 HTML 字符串的开销。

标签内的任何内容都会像普通的 HTML 一样被解析，除了:

*   它不会被渲染。
*   里面的标签不会运行。
*   它里面的标签不会被评估。
*   它不加载任何外部资源(因此您不会看到任何对`<img>`或`<embed>`标签内容的请求)。
*   可以通过`<template>`元素的特殊`content`属性将它作为一个`DocumentFragment`实例来访问。

这个属性是这里最强大的部分。`DocumentFragment`实例提供了一个 API 来操作它们的内容，这个 API 与全局`document`对象基本相同，所以你可以像操作它们自己的独立文档一样操作它们。最重要的是，与操纵元素的`innerHTML`属性(或使用`insertAdjacentHTML()`)相比，将`DocumentFragment`实例插入 DOM 要快得多，因为 DOM 结构已经存在于内存中，所以它只需要链接到 DOM 树中。

## 那么你能用它做什么呢？

一个简单的例子可能是这样的:

```
<template id='#closeTemplate'>
    <button type="button" class="close" data-dismiss='modal' aria-label="Close">
        <span aria-hidden="true">&times;</span>
    </button>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

对于以前使用过 Bootstrap 4 的人来说，这应该很容易识别。它只是为 Bootstrap 4 模式的关闭图标定义了一个模板。

```
const closeTemplate = document.querySelector('#closeTemplate').content

for (const modalHeader of document.querySelectorAll('.modal-header')) {
    modalHeader.append(closeTemplate.cloneNode(true))
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，使用这个模板，并用`modal-header`类将其内容插入到每个元素的末尾(如果您使用的是常规的 Bootstrap 4 modals，就应该在这个位置)。

这里最重要的是对模板内容的`cloneNode`方法的调用。当使用`true`作为参数调用这个方法时，会创建一个全新的`DocumentFragment`实例，它是原始实例的精确副本(这听起来是一个开销很大的操作，但实际上并没有那么糟糕，尤其是对于这样的小模板)。这一点很重要，因为给定的`DocumentFragment`只能存在于 DOM 中的一个地方，所以对任何将它插入 DOM 的方法的后续调用都将失败(由于某种我无法理解的原因，不会引发异常)。通过在每次循环迭代中创建一个副本，并将其插入 DOM 而不是原始文件，我们可以避免这个问题。

## 好吧，但是把它用于实际的模板呢？

当然，大多数模板并没有那么简单。在现实生活中，您通常必须将一些东西放入模板中才能使用，并且您通常需要确保在将它放入 DOM 之前正确设置了某些属性。

这就是拥有与全局`document`对象几乎相同的接口的`DocumentFragment`类的用武之地。这样，您可以像在页面中查找元素一样在片段上调用`querySelector`,并获得一个真实的`Element`或`NodeList`,然后您可以像在页面本身中请求元素一样操纵它们。

考虑下面的示例代码:

```
<template id='modalTemplate'>
    <div class='modal fade' tabindex='-1' role='dialog' aria-hidden='true'>
        <div class='modal-dialog' role='document'>
            <div class='modal-content'>
                <div class='modal-header'>
                    <h5 class='modal-title'></h5>
                    <button type='button' class='close' data-dismiss='modal' aria-label='close'>
                        <span aria-hidden='true'>&times;</span>
                    </button>
                </div>
                <div class='modal-body'></div>
                <div class='modal-footer'>
                    <button type='button' class='btn btn-secondary' data-dismiss='modal'>Close</button>
                    <button type='button' class='btn btn-primary'>Save</button>
                </div>
            </div>
        </div>
    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的模板是一个 Bootstrap 4 模型的基本框架。注意，这里没有 ID 属性(或者任何引用它们的属性)。模板内容仍然需要满足整个页面中 ID 属性的唯一性要求，所以最安全的做法是避免在模板本身中使用它们，并在使用模板时从代码中填充它们。

```
const modalTemplate = document.querySelector('#modalTemplate')

function createModal(id, title, body) {
    const node = modalTemplate.cloneNode(true)
    const modal = node.querySelector('.modal')
    const modalTitle = node.querySelector('.modal-title')
    const modalBody = node.querySelector('.modal-body')

    modal.id = id
    modal.setAttribute('aria-labelledby', `${id}Title`)

    modalTitle.id = `${id}Title`
    modalTitle.textContent = title

    modalBody.innerHTML = body

    document.body.append(node)

    return document.querySelector(`#${id}`)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个函数可以将模板转换成文档中的实际模态。这会将适当的`id`和`aria-labelledby`属性添加到模态的根元素，将适当的`id`和文本内容添加到标题栏，然后在将模态本身添加到文档的`<body>`元素的末尾之前，添加模态主体中需要的任何内容。

## 但是等等，还有！

因为所有这些模板构造都是通过 JavaScript 完成的，所以您也拥有了流控制逻辑可用的 JavaScript 的全部功能。您会注意到，在上面的例子中，我们使用 JavaScript 模板字符串来计算标题 ID 和模态的`aria-labelledby`属性的正确值，但是您甚至不限于此。您可以使用循环、条件甚至 try/catch 语句轻松完成复杂的流控制。

最重要的是，因为`DocumentFragment`的工作方式几乎与 DOM 相同，所以您可以将 HTML5 模板注入到其他 HTML5 模板中。例如，上面的函数可以很容易地扩展为接受模态主体的`DocumentFragment`实例，该实例本身可以从另一个 HTML 模板创建。

通过利用这两个事实，您可以毫不费力地创建由多个 HTML5 模板组成的复杂布局。

此外，模板(包括相关的 JavaScript)往往比许多 JavaScript 模板库的等效预编译模板代码更小(根据我自己的经验，它比等效预编译的 lodash 或下划线模板小 5-25%)，它们在插入 DOM 时呈现得更快，并且您不需要特殊的工具来处理构建或检查它们，因为它们只是普通的 HTML 和普通的 JavaScript。

## 但是肯定不是广泛支持的吧？

HTML5 `<template>`元素实际上得到了很好的支持。过去 3 年发布的所有主流浏览器都完全支持它，过去 5 年发布的大多数浏览器也支持它。您可以在我可以使用的[上查看准确的支持数据。](https://caniuse.com/#feat=template)。在撰写本文时，它的可用性市场份额还不到 95%。

这里需要注意的是，Internet Explorer 缺乏支持(Opera Mini 和黑莓浏览器也是如此，但在许多情况下，它们都没有足够的市场份额)。然而，有许多 polyfills 可以让你在 IE 11 中获得适当的支持，在 IE 9 和 IE 10 中获得基本的支持，并解决一些旧实现的问题(我可以使用吗？上面的链接包括几个好的 polyfills 的链接，还有一个包含在 Web 组件项目中。

现在，很明显，只有当你没有使用一个完整的应用程序框架时，这才有用。他们中的大多数自己做模板，因为这样更容易，因此当你把他们和其他模板技术混合在一起时，做得并不好。另一方面，如果除了模板之外，你不需要一个完整的应用程序框架，这可能是一个简单的选择，可以让你消除一个依赖(也可能加快你的应用程序)。