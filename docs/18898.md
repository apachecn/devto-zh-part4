# 用 React 创建 Chrome 扩展

> 原文：<https://dev.to/hurricaneinteractive/creating-a-chrome-extension-with-react-2bnm>

最近，我着手创建一个 chrome 扩展，并利用 JavaScript 和 React 将一个组件注入一个网站。结果是突变观察者和 JavaScript 的完美结合！

> 代码可以在 [Github](https://github.com/myweekinjs/chrome-extension-w-react) 上找到

## 让我们准备好！

首先，我从 chrome 开发者网站下载了一个 Chrome 扩展的启动器。如果你想学习扩展开发的基础，我绝对推荐看网站。我立即删除了`options.js`、`options.html`和`popup.js`文件。在`manifest.json`文件中，我移除了`permissions`数组中的`options_page`键和`storage`值。接下来，您想要将`https://www.myweekinjs.com/`添加到`permissions`数组中。

我将多次引用 *myweekinjs* ,这可能是你希望注入 React 组件的任何网站。

接下来，我用一个简单的`console.log`创建了一个`app.js`来测试脚本是否有效，并将`background.js`更新为；

```
chrome.runtime.onInstalled.addListener(function() {
  chrome.declarativeContent.onPageChanged.removeRules(undefined, function() {
    chrome.declarativeContent.onPageChanged.addRules([{
      conditions: [new chrome.declarativeContent.PageStateMatcher({
        pageUrl: {
          hostEquals: 'www.myweekinjs.com',
          schemes: ['https', 'http'],
          pathContains: 'inject-me'
        },
        css: ['div']
      })],
      actions: [
        new chrome.declarativeContent.RequestContentScript({
          js: ['app.js']
        })
      ]
    }]);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

好吧！太多了！`background.js`脚本将执行以下操作:

1.  倾听页面/标签的变化
2.  检查当前页面是否为(http | https)://[www.myweekinjs.com/inject-me](http://www.myweekinjs.com/inject-me)
3.  如果是，它将加载我们的`app.js`文件

按照这些[步骤](https://developer.chrome.com/extensions/getstarted#manifest)加载您的扩展以进行测试。

## 来拿脚本吧！

下一步是创建我们的`webpack.config.js`文件来编译我们的 React 和 Javascript。此时，我建议用当前文件(减去`app.js`)创建一个 *dist* 文件夹，并解压该文件夹作为我们的扩展。这样你就可以编译到这个 *dist* 文件夹中，而不会将你的 *node_modules* 包含到扩展中。

> 我们将使用这个了不起的资源来生成我们的 webpack 和。babelrc 文件 [createapp.dev](https://createapp.dev/)

1.  打开资源^
2.  检查反应，巴贝尔。取消选中 React 热加载程序
3.  运行`npm init -y`并安装资源概述的包
4.  将`webpack.config.js`和`.babelrc`文件复制到您的项目中
5.  从`package.json`中复制`scripts`

我们需要做一些小的调整。对于`webpack.config.js`改变输入和输出设置；

```
var config = {
  entry: './app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.js'
  },
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

将`build-dev`脚本更改为；

```
"dev": "webpack -d --mode development --watch" 
```

Enter fullscreen mode Exit fullscreen mode

您可能对`.babelrc`有一些语法问题，但是它们应该很容易修复，主要是关于使用双引号的问题。

运行`build-prod`脚本将编译`app.js`文件。在一次拆包和重装之后，你会看到和以前一样的`console.log`。这是一个漫长的过程，但现在事情变得有趣了！

## 来拿 appy 吧！

我们希望我们的应用程序做一些事情；

1.  等待页面完全加载
2.  观察目标容器上的突变
3.  插入我们的反应根
4.  渲染反应组件

我们从下面的结构开始。它向`load`上的窗口添加了一个监听器，并包含了我们的主回调函数，我称之为 *app* 。

```
window.addEventListener('load', function() {})

const app = () => {} 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 1 完成！向前！

接下来，我们将添加一个[变异观察器](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)，这给了我们观察 DOM 树变化的超能力。它是非常甜蜜的。对于我们的项目，我们将在我们的[测试页面](http://myweekinjs.com/inject-me)上观察`target-test` div(测试页面在我的个人博客上)。以下代码被添加到 *load* 回调中。

```
// Specifies the element we want to watch
const watch = document.getElementById('target-test')

// Creates a new Mutation Observer
const observer = new MutationObserver((mutationList, observer) => {

})

// Starts observing the child list of the element
observer.observe(watch, {
  childList: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，如果我们可以找到我们正在寻找的元素，我们希望循环遍历突变并调用我们的 app 方法。

```
const observer = new MutationObserver((mutationList, observer) => {
  // Loops through the mutations
  for (const mutation of mutationList) {
    // Checks if it is a change to the child elements
    if (mutation.type === 'childList') {
      // Attempts to find the p tag
      const target = watch.querySelector('p')
      if (target) {
        // Calls our app method
        app(observer, target)
      }
    }
  }
})

// Update the callback to accept those arguements
const app = (observer, target) => {} 
```

Enter fullscreen mode Exit fullscreen mode

快到了！现在，我们想为 React 组件创建一个根，并将其插入目标元素之前。

```
const app = (observer, target) => {
  // Disconnects from the observer to stop any additional watching
  observer.disconnect()

  // Checks if the element doesn't exist
  if (!document.getElementById('react-root-test')) {
    // Create and inserts the element before the target
    const parent = target.parentNode
    const root = document.createElement('div')
    root.setAttribute('id', 'react-root-test')

    parent.insertBefore(root, target)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们开始行动吧！

现在我们有了 React 根，我们终于可以创建和呈现我们的组件了。我将在同一个文件中创建一个简单的 React 组件。但是，您可以创建任何您选择的组件，这取决于您！一旦您添加了您的组件，解压缩您的扩展并重新加载测试页面，您应该看到组件出现了！

```
import React from 'react'
import ReactDOM from 'react-dom'

const TestComponent = () => (
  <h1>I am dynamically added!</h1> )

const app = () => {
  //...
  parent.insertBefore(root, target)

  ReactDOM.render(<TestComponent />, document.getElementById('react-root-test'))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 轰！

我们做到了！这仅仅是你使用 chrome 扩展和 React 所能做的皮毛。使用相同的技术，您将能够向网站添加功能。类似于 Grammarly 或 LastPass 这样的扩展。可能性几乎是无穷无尽的！

## 包装完毕

我认为这是一个非常酷的项目。我绝对没有想到 chrome 扩展会有这样的事情。在这个例子中使用突变观察器可能有些过头了。然而，当你遇到一个动态呈现内容的网站时，能够等到你需要的内容准备好了，这是超级酷的！如果您对代码或过程有任何问题，请通过 [Twitter](https://twitter.com/hurricane_int) 联系我，我很乐意继续讨论这个问题并改进我的代码。

* * *

谢谢你看我的文章，它真的意义重大！❤️，请提供任何反馈或意见，我总是期待改进和有意义的讨论。这篇文章是我的 [#myweekinjs](http://myweekinjs.com) 挑战的一部分，如果你有兴趣了解更多，我还有其他有趣的文章。

### 👋下次见！