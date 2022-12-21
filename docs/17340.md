# 当用户更改标签时更改您的文档标题

> 原文：<https://dev.to/hurricaneinteractive/guilt-users-into-coming-back-with-your-document-title-55kn>

**没有介绍只是[进入代码](#code)T3】**

## 我们开始吧

首先，我们需要一个项目基地。为此，我将使用[创建-反应-应用](https://github.com/facebook/create-react-app)。然而，代码可以在任何 JavaScript 项目中使用。我们只需要访问`document`对象。

> 这个项目的代码可以在 [Github](https://github.com/myweekinjs/please-dont-leave) 上找到

### 第一步:初始化

```
npx create-react-app please-dont-leave
cd please-dont-leave
npm start || yarn start 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:查找文件

打开您的`App.js`文件。

### 第三步:魔法

在`App`函数之上创建一个名为`getBrowserHiddenProps`的函数。这个函数将根据我们使用的浏览器获取正确的属性。

```
const getBrowserHiddenProps = () => {
  let hidden, visibilityChange
  if (typeof document.hidden !== "undefined") {
    hidden = "hidden";
    visibilityChange = "visibilitychange";
  } else if (typeof document.msHidden !== "undefined") {
    hidden = "msHidden";
    visibilityChange = "msvisibilitychange";
  } else if (typeof document.webkitHidden !== "undefined") {
    hidden = "webkitHidden";
    visibilityChange = "webkitvisibilitychange";
  }

  return {
    hidden,
    visibilityChange
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用一种简化的方法返回一个包含`hidden`和`visibilityChange`值的对象。通过不定义对象的键，这些键将默认为变量名。下面将达到与上面相同的效果。

```
return {
  hidden: hidden,
  visibilityChange: visibilityChange
} 
```

Enter fullscreen mode Exit fullscreen mode

很酷吧？无论如何，**开始吧！**

下一步是向文档添加一个事件侦听器，以检查页面可见性是否发生了变化。我们将在`App`函数中完成这项工作。

```
const { hidden, visibilityChange } = getBrowserHiddenProps()

if (typeof document.addEventListener !== "undefined" && typeof hidden !== "undefined") {
  // We can continue
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有另一个理由将`hidden`和`visibilityChange`作为对象返回。这是为了让我们能够[解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring)T2 函数的返回值。我们还检查以确保我们能够向文档对象添加一个事件监听器，并确保`hidden`值不是`undefined`。

接下来，我们需要添加基于可见性事件(`visibilityChange`)的事件监听器。

```
const { hidden, visibilityChange } = getBrowserHiddenProps()

if (typeof document.addEventListener !== "undefined" && typeof hidden !== "undefined") {
  // We can continue
  document.addEventListener(visibilityChange, () => {

  }, false)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以检查`hidden`值和它的行为。在我们的例子中，我们会要求用户回来，因为我们错过了他们的注意力。

```
const { hidden, visibilityChange } = getBrowserHiddenProps()

if (typeof document.addEventListener !== "undefined" && typeof hidden !== "undefined") {
  // We can continue
  document.addEventListener(visibilityChange, () => {
    if (document[hidden]) {
      document.title = "😭 PLEASE COME BACK!!"
    } else {
      document.title = "😍 YAY!"
    }
  }, false)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！当您更改标签时，观察您的文档标题的变化。**成功**

### 第四步:可能性

现在，更改标签标题可能没什么用，但是，当用户移动到另一个标签时，您可以做一些事情。这样做的一个最有用的原因是发送一个 Google Analytics 事件。这将允许你开始看到用户何时离开你的页面，如果他们回来。非常酷。

这些代码中的很多都是从 MDN 上的[页面可见性 API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API) 页面获得的。如果你感兴趣的话，我建议你去看看这个页面，了解更多的信息。

* * *

谢谢你看我的文章，它真的意义重大！❤️，请提供任何反馈或意见，我总是期待改进和有意义的讨论。

### 👋下次见！