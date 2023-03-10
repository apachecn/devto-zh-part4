# 在浏览器中编辑和上传文件的麻烦

> 原文：<https://dev.to/pqina/the-trouble-with-editing-and-uploading-files-in-the-browser-4a7p>

随着文件 API 的引入，我们获得了在浏览器中编辑文件的能力。我们最终可以调整图像大小，在浏览器中解压缩文件，并基于浏览器中的交互生成新文件。不过有一点需要注意，你不能上传这些文件。

你可以，但是你必须求助于`XMLHttpRequest`或者`fetch`，看，不可能设置文件输入元素的值。这意味着您不能在提交经典表单的同时提交自定义文件，您必须异步上传文件。这实际上阻止了任何文件编辑的渐进增强解决方案。如果您决定在客户机上修改一个文件，那么您还必须确保在服务器上进行更改，以便可以接收修改后的文件。

作为一个开发图像编辑产品的产品开发人员，这真的让我很累。我很乐意将我的产品作为仅客户端解决方案提供。但是这是不可能的，因为异步文件上传需要服务器端的修改。 [WordPress](https://wordpress.org/) ， [Netlify](https://www.netlify.com/) ， [Shopify](https://www.shopify.com/) ， [Bubble.io](https://bubble.io/) ，它们都提供了默认的文件输入元素，但是如果不编写一个客户端和服务器端的插件，就没有直接的方法来支持它们。在 WordPress 的例子中，这意味着为每个表单生成器提供一个插件。不太现实。

但是几个月前发生了一些变化。

> 本文最初发表于[我的个人博客](https://www.pqina.nl/blog/the-trouble-with-editing-and-uploading-files-in-the-browser/)

## 将自定义文件设置为文件输入

我们不能设置文件输入元素的`value`，这真的很符合逻辑。这样做将允许我们将它指向访问者文件系统中的文件。

```
<input type="file">

<script>
document.querySelector('input').value = 'some/file/i/want/to/have';
</script> 
```

Enter fullscreen mode Exit fullscreen mode

显然这是一个巨大的安全隐患。

设置文件输入`value`属性在表中；

文件输入`files`属性呢？如果我们能以某种方式更新 files 属性或更新其中的文件，这将解决问题。

`files`属性保存对一个 [`FileList`](https://developer.mozilla.org/en-US/docs/Web/API/FileList) 的引用。太好了！让我们创建一个`new FileList()`并覆盖文件输入中的那个。不幸的是没有`FileList`构造函数。在`FileList`实例上也没有暴露*【添加文件】*方法。最重要的是，`File`对象没有更新文件数据的方法，所以我们不能更新`files`列表中的单个文件对象。

那就这样吧。

直到几个月前，一个 go[Hidde Vries](https://hiddedevries.nl/en)把我带到了[WHATWG](https://github.com/whatwg/html/issues/3269)上的这个问题，结果是我们可以使用不同的 API 来实现我们的目标。

Firefox 和 Chrome 最近都增加了对`DataTransfer`构造函数的支持。`DataTransfer`类最常用于从用户设备向网页拖放文件。

它有一个类型为`FileList`的`files`属性🎉

它还有一个`items.add`方法用于向列表中添加条目🎉

哦啦啦！

```
<input type="file">

<script>
// Create a DataTransfer instance and add a newly created file
const dataTransfer = new DataTransfer();
dataTransfer.items.add(new File(['hello world'], 'This_Works.txt'))

// Assign the DataTransfer files list to the file input
document.querySelector('input').files = dataTransfer.files;
</script> 
```

Enter fullscreen mode Exit fullscreen mode

[CodePend 上的现场演示](https://codepen.io/rikschennink/pen/WNezEjK?editors=1011)

它只是工作。太棒了。我们现在有了一种方法，可以将在客户机上创建的文件发送到服务器，而不必对服务器端 API 进行任何更改。

然而。*悲伤的长号*。这在 IE、Edge、Safari 13 上都不行。

## 其他浏览器的替代品

如果我们想在提交表单时提交文件数据，我们能在其他浏览器上为用户提供什么呢？目前我能想到的替代方案有两个。一个需要在服务器上进行更改，另一个可能会有问题，这取决于您的用例。

让我们看一看。

*   [对文件数据进行编码](#encode-the-file-data)
*   [捕获表单提交](#capture-the-form-submit)

### 对文件数据进行编码

我们可以将文件数据编码为 base64 字符串或 dataURL，将结果字符串存储在一个隐藏的 input 元素中，然后在提交表单时发送它。这将需要对服务器进行更改，服务器必须意识到编码文件也可能被提交。服务器还必须解码 dataURL，并将其转换回文件对象。

我们可以使用`FileReader` API 将一个`File`转换成一个`dataURL`。

```
<input type="file">
<input type="hidden">

<script>
document.querySelector('input[type="file"]').onchange = e => {
    const reader = new FileReader();
    reader.onloadend = () => {
        document.querySelector('input[type="hidden"]').value = reader.result;
    };
    reader.readAsDataURL(e.target.files[0]);
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我的客户在使用这种方法时报告了几个问题。

*   服务器上运行的监控流量的安全相关脚本可能会将表单 post 标记为可疑，因为它包含大量基于字符串的数据。
*   当提交大文件，也就是 1MB 以上的文件时，浏览器很可能会出现“内存不足”的错误。这因浏览器而异，但我在手机和桌面浏览器上都见过。
*   您在文件输入中看不到变化。因此，在提交表单时，最好重置、禁用或隐藏它。

如果你处理的是小图片，任何大于 1MB 的文件，编码文件是一个很好的解决方案，我会避开它。

### 捕获表单提交

我们**可以在异步提交表单时**添加定制文件。所以另一个解决方案是捕获整个表单提交，并使用`XMLHttpRequest`或`fetch`将表单异步提交到同一个端点(`action`属性)。

这就是我尝试用 [Poost](https://github.com/rikschennink/poost/) 做的事情(这很大程度上是一个原型，而且我不擅长当场命名)。Poost 捕获表单 submit，然后异步提交表单。这允许我们构建一个定制的 [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 对象，添加我们的定制文件(存储在`_value`属性中)而不是`files`属性中的文件。

```
<input type="file">

<script>
// Create a new File object
const myFile = new File(['Hello World!'], 'myFile.txt', { type: 'text/plain', lastModified: new Date() });

// Assign File to _value property
const target = document.querySelector('input[type="file"]');
target._value = [myFile];
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这实际上工作得很好。我们将相同的数据发送到相同的端点。当您意识到返回的页面也需要呈现到屏幕上(通常浏览器导航到它)时，事情就变得棘手了。我们将在哪里渲染它，如何处理导航器历史，如何处理页面上的脚本标签，IE 怎么办(这一点也不奇怪)。

*   同样，当设置`_value`时，你看不到文件输入的变化。因此，在提交表单时，最好重置、禁用或隐藏它。
*   我们接管了很多默认的浏览器行为，这总是导致灾难。

尽管如此，对于非常基本的形式，这很好地工作。你不必修改服务器，甚至可以有条件地加载它，作为浏览器不支持`new DataTransfer()`时的后备。

## 事物的状态

因此，尽管我们的文件上传情况有所改善，但仍然非常好。

由于 IE、Edge 和 Safari，我们仍然坚持使用这些创可贴解决方案。如果您有足够的时间，在服务器上进行修改以促进异步传输可能会更容易。如果你的情况是不可能的，我希望上面提供的解决方案可能正好适合你的情况，帮助你。

如果你有什么要补充的，请在下面分享。