# 用 babel 传输 JavaScript 代码的陷阱

> 原文：<https://dev.to/bseber/pitfalls-of-transpiling-javascript-code-with-babel-5c4c>

# 用巴别塔传输 JavaScript 代码的陷阱

最近我有一个这样的 WTF？！？时刻。我将 webpack 依赖项从 4.28.4 更新到 4.39.3。单元测试仍然通过。我启动了应用程序，但“什么都不用”了。浏览器控制台显示类似`Uncaught RangeError: Maximum call stack size exceeded`的错误日志。发生了什么事？

在我们的项目中，我们使用

*   将现代 JavaScript 代码转换成可以在传统浏览器上运行的 JavaScript。
*   `date-fns`用于日期处理的库。
*   `babel-plugin-date-fns`优化最终的 JavaScript 包文件大小。这个插件用`import isToday from 'date-fns/is-today'`代替了进口的`import { isToday } from 'date-fns'`。这导致最终的包不包含整个库，而只包含来自 date-fns 的所需内容。(从支持树抖动的 fns 版本 2 开始过时)

让我先给你看一些代码片段。

原始代码:

```
import { isToday, isPast } from "date-fns"

const assert = {
  isPast: function(date) {
    return !isToday(date) && isPast(date);
  },
}; 
```

直到最近才编译的代码(webpack 4.28.4，模式=开发):

```
var date_fns_is_today__WEBPACK_IMPORTED_MODULE_9__ = __webpack_require__(/*! date-fns/is_today */ "MNHD");
var date_fns_is_today__WEBPACK_IMPORTED_MODULE_9___default = /*#__PURE__*/__webpack_require__.n(date_fns_is_today__WEBPACK_IMPORTED_MODULE_9__);
var date_fns_is_past__WEBPACK_IMPORTED_MODULE_10__ = __webpack_require__(/*! date-fns/is_past */ "qTUo");
var date_fns_is_past__WEBPACK_IMPORTED_MODULE_10___default = /*#__PURE__*/__webpack_require__.n(date_fns_is_past__WEBPACK_IMPORTED_MODULE_10__);

var assert = {
  isPast: function isPast(date) {
    return !date_fns_is_today__WEBPACK_IMPORTED_MODULE_9___default()(date) && date_fns_is_past__WEBPACK_IMPORTED_MODULE_10___default()(date);
  },
} 
```

更新 webpack(到 4.39.3，模式=开发)后的 Transpiled 代码:

```
var date_fns_is_today__WEBPACK_IMPORTED_MODULE_9__ = __webpack_require__(/*! date-fns/is_today */ "MNHD");
var date_fns_is_today__WEBPACK_IMPORTED_MODULE_9___default = /*#__PURE__*/__webpack_require__.n(date_fns_is_today__WEBPACK_IMPORTED_MODULE_9__);

var assert = {
  isPast: function isPast(date) {
    /* NOTE: Today is not in the past! */
    return !date_fns_is_today__WEBPACK_IMPORTED_MODULE_9___default()(date) && isPast(date);
  },
} 
```

巴别塔配置:

```
{  "babel":  {  "presets":  [  [  "@babel/preset-env",  {  "modules":  false  }  ]  ],  "plugins":  [  "babel-plugin-date-fns"  ],  "env":  {  "test":  {  "presets":  [  [  "@babel/preset-env",  {  "targets":  {  "node":  "current"  }  }  ]  ]  }  }  } 
```

你看到失败了吗？当你知道原始代码的时候并不是很明显。如果你没有发现问题，即使现在再看代码，我也会很快为你解决这个难题。但是首先我想回顾一下 WTF 会议。

除了 webpack 之外，我还更新了多个其他依赖项。独立提交中的每个依赖项(块)。每个提交都通过了单元测试。

*   网络包
*   巴别塔
*   ...

然后我启动应用程序，一个奇妙的`Uncaught RangeError: Maximum call stack size exceeded`错误出现在浏览器中。

由于事实上在这一天发生了类似的问题，我立即知道这只能是巴别塔更新或网络包更新。(请注意，这是浏览器的运行时问题。单元测试仍然有效！因此，它必须是一个 transpilation 问题。)由于原子提交，很容易发现 webpack 更新提交是罪魁祸首。检查两个提交并启动应用程序。很简单。

但是下一步应该怎么做呢？谷歌一下这个问题？使用哪个查询？搜索“webpack 更新后超出最大调用堆栈大小”没有找到有用的文章。

所以我去了 github，克隆了 webpack 项目。我知道它适用于 webpack 4 . 28 . 4，但不适用于 4.39.3。我已经将本地 web pack 链接到项目中，并使用它来构建应用程序。对于`git bisect`,我必须构建大约 6 次才能找到邪恶的提交

```
 > git checkout v4.39.3
  # start a binary search for the commit that breaks something
  > git bisect start
  # we know that it's broken here
  > git bisect bad
  # we know that it worked recently
  > git checkout v4.28.4
  > git bisect good
  # git now starts checking out commits between the previously marked commits
  # you can go through theses commits and mark it with 'good' or 'bad' 
```

我看着 webpack 提交的 [diff，心想“好吧，我不知道你在这里做什么。但是注释“在递归调用的范围内添加函数名”听起来像是运行时错误。递归==最大调用堆栈"](https://github.com/webpack/webpack/commit/eb9e875c129a4e65fb286dffe36b4b8b485a2764)

我看着链接的[错误报告](https://github.com/webpack/webpack/issues/8874)，心想“好吧...我仍然没有线索，但它似乎是正确的，不是吗？但为什么这会破坏我的申请？我不重用外部作用域的变量名。甚至被[eslint/无影法则](https://eslint.org/docs/rules/no-shadow)
验证

```
 import { isToday, isPast } from "date-fns"

  const assert = {
    isPast: function(date) {
      // no shadowing here
      // `assert.isPast` is something else and not called here
      // `isPast` is the imported function
      return !isToday(date) && isPast(date);
    },
  }; 
```

但是传输的代码包含了阴影...随着 webpack 错误修复提交，`isPast`函数不再被取代🤪

```
 var date_fns_is_today__WEBPACK_IMPORTED_MODULE__ = __webpack_require__("date-fns/is_today");
  var date_fns_is_isPast__WEBPACK_IMPORTED_MODULE__ = __webpack_require__("date-fns/is_past");

  var assert = {
    // function is not anonymous anymore
    // it is declared as 'isPast'
    isPast: function isPast(date) {
      return !date_fns_is_today__WEBPACK_IMPORTED_MODULE_9__(date) && isPast(date);
    },
  }; 
```

我问自己的下一个问题是“谁给匿名函数添加了名字？”

嗯，我自己用`@babel/preset-env`配置了巴别塔，它将`isPast: function() {}`转换为`isPast: function isPast() {}`。为什么你会问？嗯，我认为这是由于更好的堆栈跟踪。原始代码声明了一个匿名函数并将其分配给键`isPast`，而后面的代码声明了一个命名函数，然后该函数被分配。不同之处在于，老版本的浏览器在错误情况下记录的是`<anonymous>:1337:42"`，而不是`<isPast>:1337:42"`。

如果我们配置`@babel/preset-env`只为现代浏览器如 firefox@68 传输原始代码，它将只获取原始代码。现代浏览器 devtools 仍然显示堆栈跟踪中的`anonymous`部分，使用指定的键名。

那么为什么单元测试没有适得其反呢？为什么他们还是通过了？你注意到巴别塔配置中的`env`部分了吗？我们使用`jest`作为测试跑步者。`jest`将`NODE_ENV`环境变量设置为`test`。然后，Babel 被配置为针对当前 nodejs 版本进行代码编译。猜猜上面两个片段中的哪一个选项将会为 nodejs 进程中运行的单元测试生成？是的，你是对的。

但是为什么我们有两种不同的代码翻译配置呢？很高兴你问了！因为我们写的是浏览器的代码，不是 nodejs 的。我们仍然在 nodejs 环境中用 jsdom 实现的模拟 DOM 抽象来测试它...我们在浏览器中使用 jasmine 测试所有东西的美好时光😅无论如何，使用 node 和 jsdom 是有充分理由的。至少对于单元测试来说是这样。执行时间快得多，我们不需要浏览器设置(html 文件，浏览器二进制文件，...).然而，回到`@babel/preset-env`配置。使用`"target": { "node": "current" }`是最便捷的方式。而且在大多数用例中它只是工作，对吗\ *(ツ)* /

## 修修补补

老实说，我不知道如何解决这个问题。也许是`babel-plugin-date-fns`的一个 bug。可能是`babel-preset-env`的 bug 吧。我没打算在兔子洞里越陷越深。

在我的用例中，随着`date-fns`升级到版本 2，这个问题已经过时了。此次重大更新支持摇树。所以我可以从我的构建配置中删除`babel-plugin-date-fns`,然后编译后的代码访问`date-fns`模块，而不是单独的函数名。

```
var date_fns__WEBPACK_IMPORTED_MODULE__ = __webpack_require__("date-fns");

var assert = {
  isPast: function isPast(date) {
    return !Object(date_fns__WEBPACK_IMPORTED_MODULE_2__["isToday"])(date) && Object(date_fns__WEBPACK_IMPORTED_MODULE_2__["isPast"])(date);
  },
}; 
```

不幸的是，这个范围遮蔽问题也可能出现在 babel 插件中，比如`babel-plugin-lodash`或者其他我还不知道的 util 库。如果 lib 不支持树抖动，你必须使用 babel 插件，当然你可以使用命名函数:

```
import { isToday, isPast } from 'date-fns';

const assert = {
  isPast: function assertIsPast(date) {
    return !isToday(date) && isPast(date);
  }
} 
```

使用[简写方法名称](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Method_definitions)可能是另一种解决方案。然而，它创建了更多的代码，因为这个函数被绑定到对象上下文。

```
isPast: function (_isPast) {
  function isPast(_x) {
    return _isPast.apply(this, arguments);
  }

  isPast.toString = function () {
    return _isPast.toString();
  };

  return isPast;
}(function (date) {
  return !date_fns_is_today__WEBPACK_IMPORTED_MODULE_()(date) && date_fns_is_past__WEBPACK_IMPORTED_MODULE_()(date);
}), 
```

对比

```
isPast: function isPast(date) {
  return !date_fns_is_today__WEBPACK_IMPORTED_MODULE_()(date) && date_fns_is_past__WEBPACK_IMPORTED_MODULE_()(date);
}, 
```

## 吸取教训

现代 JavaScript 开发棒极了。我们有所有这些伟大的工具来编写带有语法糖的代码，还有尚未被所有浏览器平台实现的新特性。

然而，作为一名优秀的软件工程师...了解您的工具，了解您的代码库会发生什么。了解您的构建设置。进行原子提交。在合并请求中引用问题票证。

*   `git bisect`帮助我快速找到 webpack 中的错误提交
*   错误标签的参考解释了错误修复的“原因”
*   不在生产环境中运行测试，并且不使用实际的捆绑源代码...百米...我想总比没有好🤔然而，我必须再次研究在真正的浏览器运行时运行我的单元测试