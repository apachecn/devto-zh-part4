# WTF 是巴别塔和网络包😵？两分钟内解释完毕。

> 原文：<https://dev.to/getd/wtf-are-babel-and-webpack-explained-in-2-mins-43be>

Babel 和 Webpack 是苹果🍎和香蕉🍌。当他们解决不同的问题时，人们从不把他们放在一起比较。这篇文章解释了他们不同的概念。

# 通天塔

Babel 只是一个翻译器，它把你的‘花哨’(ES6+)JS 代码翻译成 browser(前端)或 Node.js(后端)理解的‘不那么花哨’(ES5)的代码。

为什么我们说的比 browser 和 Node.js 更 fancy？因为我们迫不及待地想使用最新最棒的，甚至在它们得到官方支持之前。

下面是当今大多数开发人员编写的奇特代码。不管它有多花哨，我们的 browser / Node.js 都不知道它在说什么。*(注:现在部分 Node.js 更高版本已经支持 ES6。)*

```
// ES6 syntax
import moment from 'moment';

export default () => moment().format("YYYY Do MM"); 
```

这就是为什么我们需要 Babel 将上面的代码翻译成下面的普通代码，让我们的 browser / Node.js 能够理解。

```
// ES5 syntax
const moment = require('moment')

function getDateString() {
  const date = moment();
  return date.format("YYYY Do MM");
}

exports.default = getDateString; 
```

这就是为什么巴别塔有时被称为运输者。

值得注意的是，Babel 通常用于前端和后端。我为什么要提这个？因为 Webpack 只是前端的(大多数情况下)。

# Webpack

如果 Babel 是 JS 的翻译器，你可以把 Webpack 想成是一个可以处理各种语言(或资产)的巨型多翻译器。例如，Webpack 经常将运行 Babel 作为其工作之一。另一个例子，Webpack 可以收集 Javascript 文件中的所有内联 CSS 样式，并将它们捆绑成一个。

**为什么我们前端需要这样一个庞然大物，后端却不需要？**

因为前端有许多种类的资产，如 CSS，SASS，图像，字体，并且比只有 JS 的后端更复杂和动态。最终，我们需要以某种方式将各种资产打包到一个小文件中，以便用户的浏览器可以在页面加载时下载。这也被称为`minify`和`uglify`。你看，后端没有上述要求。

另一个重要原因是前端不支持模块(同样，在大多数情况下)。模块是 Node.js 的内置特性，不是浏览器。现在的开发者已经很习惯前端的`npm install`、`import`和`export` JS 模块，因为它让我们可以更好地组织代码和共享包。但实际上它们只是语法上的糖，Webpack 的工作是找出我们在代码中使用的所有模块之间的所有依赖关系，并将它们编译成浏览器实际理解的一大块 JS 代码。

我们什么时候在后端使用 Webpack？一个很好的用例是支持 SSR(服务器端渲染)。

# 总而言之，

*   后端:我们使用 Babel，这样我们就可以对 Node.js 使用最先进的 JS 语法(ES6/7)
*   前端:我们使用 Webpack(它使用 Babel 和其他东西)将 JS 代码和许多其他资产编译成几个小的捆绑文件，用户可以在第一次加载我们的网页时下载这些文件。例如， [`create-react-app`在创建你的应用时使用 Webpack 和 Babel](https://github.com/facebook/create-react-app#get-started-immediately) 。