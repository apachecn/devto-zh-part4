# 用彩色文本重新创建图像

> 原文：<https://dev.to/ashleemboyer/re-create-an-image-with-colorful-text-2lmk>

*这篇文章最初发表在[我的博客](https://ashleemboyer.com/re-create-an-image-with-colorful-text)上。*

嘿，朋友们！有一段时间了，但我带着一些有趣的东西回来了。昨天，我在推特上发布了一张我分析过的图片，然后用我的名字重新制作了这张图片。它引起了相当多的关注，所以我想我应该告诉每个人我是如何做到的！它涉及 HTML `canvas`元素和一点点循环数据，以获取图像中的颜色信息。我们开始吧！

快速提示:在每一步的末尾都会有一个 CodeSandbox，这样你就可以一边走一边检查你的工作。

## 目录

1.  设置您的 HTML 和 JS
2.  在`canvas`上绘制您的图像
3.  拿到`ImageData`
4.  迭代获取像素颜色
5.  添加一些容器
6.  资源

* * *

## 1。设置你的 HTML & JS

这一步挺无聊的。完成此处的操作后，页面上将不会显示任何内容。有两点需要注意:

*   如果您在代码沙箱中工作，您只需要将`crossorigin`属性设置为`Anonymous`。否则会出现安全错误。
*   在图像加载之前，我们实际上不能对它做任何事情。确保所有即将到来的代码都是在`onload`事件监听器中编写的！

[https://codesandbox.io/embed/step1-fvnqc?module=index.js](https://codesandbox.io/embed/step1-fvnqc?module=index.js)

## 2。在`canvas`上绘制您的图像

既然我们已经加载了图像并且页面上有了`canvas`，我们需要为我们的画布获取 2D 渲染上下文。我们将在上下文中调用`drawImage()`,并传入三个参数:

*   我们想要绘制的图像
*   要绘制的 x 坐标
*   要绘制的 y 坐标

在这一步中，我们只需添加两行。你的图像现在应该出现了！:)

[https://codesandbox.io/embed/step2-6frr9?module=index.js](https://codesandbox.io/embed/step2-6frr9?module=index.js)

## 3。拿到`ImageData`

我们差不多到了这个过程中比较棘手的部分，但是首先我们需要从上下文中获取一些数据。我们把这个放在一个`try...catch`里，以防任何事情出错。例如，在这里您会看到由于没有设置图像的`crossorigin`属性而导致的安全性错误。

`getImageData()`函数有 4 个参数:

*   开始读取的 x 坐标
*   开始读取的 y 坐标
*   要读取的区域的宽度
*   要读取的区域的高度

请注意，我们是从整个画布上读取的，因为图像非常小，这就是我们如何设置所有内容的。如果您愿意，您可以在画布上绘制一个更大的图像，然后通过修改这 4 个参数从较小的区域读取数据。

[https://codesandbox.io/embed/step3-1b380?module=index.js](https://codesandbox.io/embed/step3-1b380?module=index.js)

## 4。迭代获取像素颜色

我们在前面得到的`ImageData`对象有三个属性:`data`、`width`和`height`。我们对第一个感兴趣。这就是事情变得有点奇怪的地方。`data`属性是一个平面数组。不知道那是什么意思？这里有一个简单的例子:

*   一个平面阵列:`["a", "b", "c", "d", ...]`
*   不是平面数组:`["a", "b", ["c", "d"], ...]`

对于从上下文中读取的数据的每个像素，有 4 条数据被推送到这个数组中，它们与该像素的颜色有关:颜色的`red`、`green`、`blue`和`alpha`值。因此，我们的图像是`200px`宽和`200px`高，这个数组应该有 160，000 条数据(因为 200 x 200 x 4 = 160，000)。

有了这些知识，我们可以以 4 为增量循环遍历`ImageData.data`,并抓取每一段颜色数据，放入我们自己的数据结构中。我们将在下一步中使用这个新结构化的数据。您可以将这一部分重新格式化为对您最有意义的格式。也许 JSON 对象更容易理解。试试看！:)

[https://codesandbox.io/embed/step4-rql19?module=index.js](https://codesandbox.io/embed/step4-rql19?module=index.js)

## 5。添加一些容器

现在我们准备好使用我们的颜色数据了！请记住，我们已经将它们存储在一个二维数组中，所以我们将不得不做一些嵌套循环。连续显示你想要的字符串的正确字母(我用了我的名字！)，我们需要记录已经添加到页面中的字母数量。我们使用余数(`%`)操作符多次换行。

这里有一堆 CSS 使得这段代码看起来有点恶心。如果我们不想让`font-size`像我指定的那样动态工作，我们*可以*把它移到一个 CSS 文件中。但是在这个演示中，我做了这个，这样你就可以调整字体的大小来“放大”或缩小你的图像。我们 CSS 中最重要的属性是`color`！我们将把它设置为上一步中得到的`RGBA`值。

这将需要几秒钟来渲染。像素有一吨(16 万)！

[https://codesandbox.io/embed/step5-xuw2j?module=index.js](https://codesandbox.io/embed/step5-xuw2j?module=index.js)

## 6。资源

就是这样！如果您对我们讨论的一些主题有疑问，请阅读以下链接。它们都在 MDN 文档中。

*   [余数(%)运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder)
*   [图形画布元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)
*   [画布渲染上下文 2D](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D)
*   [canvasrenderingcontext 2d . getimagedata()](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/getImageData)
*   [图像数据](https://developer.mozilla.org/en-US/docs/Web/API/ImageData)

如果你已经做到了这一步，恭喜你！！！🎉我希望这篇文章对你有所帮助和娱乐。如果你用这个做了一些很酷的东西，请[给我发一条推特](https://twitter.com/ashleemboyer)让我看看！

* * *

你知道我有时事通讯吗？📬

如果你想在我发布新的博客帖子或宣布重大项目时得到通知，请联系 https://ashleemboyer.com/newsletter。