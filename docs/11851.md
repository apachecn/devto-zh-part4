# 用 ES6 JavaScript 代理模仿可链接的 API

> 原文：<https://dev.to/davidwells/mocking-chainable-apis-with-es6-javascript-proxies-mh4>

> 最初发布于 [davidwells.io](https://davidwells.io/blog/mocking-chainable-apis-with-js-proxies) 🎉

ES6 代理相当疯狂。

代理给了你拦截对象调用的能力，并且可以做任何你想做的事情🤯。

我强烈推荐看看这个来自`immer`的创作者[米歇尔·维斯特雷](https://twitter.com/mweststrate)的视频，一个关于 ES6 代理的超级深度潜水和你可以用它们做的一切。

他警告说，当心代理，它们很酷，但可能会导致一些反向调试问题。

任何人，到用例上...

## 如何模仿一个可链接的 API

我发现需要为`chalk`模块替换`netlify-cli`中的可链接 API。

我们需要一个全局机制来禁用用`chalk`制作的终端颜色。

粉笔上有这个的[设定，但这是一条简单的出路。我们是开发商，我们**必须**重新发明轮子。而且，不适合我的粉笔版本...](https://github.com/chalk/chalk/tree/1f77953f1a358fa8f626f0fd872792d63da6d58a#chalklevel)

因此...让我们尝试一些代理！

首先，什么是可链 API？

```
// Methods can chain together in any order
chalk.blue.bgRed.bold('Hello world!') 
```

有许多库允许这种类型的灵活性。

你实际上可以[使用代理创建可链接的 API](https://www.keithcirkel.co.uk/metaprogramming-in-es6-part-3-proxies/)

## 代理时间

感谢 [safe-object-proxy](https://github.com/ktsn/safe-object-proxy) ，我找到了一个代理实现，它可以确保如果对象上的键不存在，对象永远不会抛出错误。

所以不再这样:

```
whatever.haha.yolo()
// Uncaught TypeError: Cannot read property 'yolo' of undefined 
```

相反，代理会神奇地让函数返回`null`。

**相当酷**

有一个类似的项目叫做 [nevernull](https://www.npmjs.com/package/nevernull) ,如果你在浏览器中运行代理的话，它会附带 polyfills。

通过一点调整、控制台日志记录和在 WTF 代理上挠头，我设法让可链接 API 无论如何都返回我的值。

成功🎉

```
// safeChalk.js
const chalk = require('chalk')

/**
 * Chalk instance for CLI
 * @param  {boolean} noColors - disable chalk colors
 * @return {object} - chalk instance or proxy noOp
 */
module.exports = function safeChalk(noColors) {
  // if no colors return chainable "noOp" API
  if (noColors) {
    return NeverNull(chalk)
  }
  // else return normal chalk library
  return chalk
}

/* Chalk NoOp proxy */
function NeverNull(obj) {
  function match(some, none = noOp) {
    return obj != null ? some(obj) : none()
  }
  return new Proxy((some, none) => {
    /* Here was my tweak to make this work with chalks chainable API */
    if (some) return some

    if (!some && !none) return obj
    return match(some, none)
  }, {
    get: (target, key) => {
      const obj = target()
      if (obj !== null && typeof obj === 'object') {
        return NeverNull(obj[key])
      } else {
        return NeverNull()
      }
    },
    set: (target, key, val) => {
      const obj = target()
      if (obj !== null && typeof obj === 'object') {
        obj[key] = val
      }
      return true
    }
  })
}

function noOp() {} 
```

然后使用它

```
const safeChalk = require('./safeChalk')
/**
 * Usage
 */
const disableChalkColors = true
const myChalk = safeChalk(disableChalkColors)

console.log(myChalk.blue.bgRed.bold('Hello world!'))
// 'Hello world!' no coloring

const normalChalk = safeChalk()

console.log(normalChalk.blue.bgRed.bold('Hello world!'))
// 'Hello world!' blue text with red BG that is BOLD 
```

我们做到了！可链 API 无论如何都管用！

## 包装完毕

正如您所看到的，这个代理很小，但是非常强大。

我建议听从迈克尔关于代理人的警告。它们有点“神奇”,调试代理看起来不是时候。

**您还见过哪些代理的使用案例？**在下面留下评论