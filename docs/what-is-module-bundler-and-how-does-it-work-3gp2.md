# 什么是模块捆绑器，它是如何工作的？

> 原文：<https://dev.to/tanhauhau/what-is-module-bundler-and-how-does-it-work-3gp2>

## 什么是模块捆绑器？

[![module bundlers](img/33d553986775c8e984142892ce68038b.png "Module Bundlers: (left to right) Rollup, FuseBox, webpack, parcel")](https://res.cloudinary.com/practicaldev/image/fetch/s--3ziHt_jf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lihautan.com/static/ece4b04e88f84e6d61abc84e759c0916/426f8/module-bundlers.png)

模块捆绑器是前端开发人员用来将 [JavaScript 模块](https://lihautan.com/javascript-modules/)捆绑成可以在浏览器中执行的单个 JavaScript 文件的工具。

现代模块捆扎机*(排名不分先后)*的例子有: [webpack](https://webpack.js.org) 、 [rollup](https://rollupjs.org) 、 [fusebox](https://fuse-box.org) 、[package](https://parceljs.org)等。

需要模块捆绑器，因为:

*   浏览器不支持模块系统，[尽管现在这并不完全正确](https://philipwalton.com/articles/using-native-javascript-modules-in-production-today/)
*   它帮助你管理代码的依赖关系，它会按照依赖顺序为你加载模块。
*   它帮助你按照依赖顺序加载你的资源，图片资源，css 资源等等。

举例来说，假设您正在构建一个 web 应用程序，它由多个 JavaScript 文件组成。你通过脚本标签将 JavaScript 文件添加到 html:

```
<html>
  <script src="/src/foo.js"></script>
  <script src="/src/bar.js"></script>
  <script src="/src/baz.js"></script>
  <script src="/src/qux.js"></script>
  <script src="/src/quux.js"></script>
</html> 
```

每个文件需要一个单独的 http 请求，这是 5 次往返请求，以便启动您的应用程序。所以如果能把 5 个文件全部合并成 1:
就更好了

```
<html>
  <script src="/dist/bundle.js"></script>
</html> 
```

*(虽然有了 [HTTP/2](https://developers.google.com/web/fundamentals/performance/http2/) ，现在这种情况少多了)*

那么我们如何生成`dist/bundle.js`？

在这一过程中出现了几个挑战:

*   我们如何**维护要包含的“文件”的顺序**？
    *   如果“文件”之间存在某种依赖关系，那就太好了
*   我们如何**防止“文件”之间的命名冲突**？
*   我们如何确定捆绑包中任何未使用的“文件”？

如果我们知道每个文件之间的关系，例如:

*   哪个文件依赖于另一个文件？
*   文件公开的接口是什么？和
*   哪些公开的接口正在被另一个使用？

这些信息，理所当然，可以解决各自带来的挑战。所以，我们需要的是一个声明性的方法来描述文件之间的关系，这就把我们带到了 [JavaScript 模块系统](https://lihautan.com/javascript-modules/)。

[CommonJS](https://requirejs.org/docs/commonjs.html) 或 [ES6 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)为我们提供了指定我们所依赖的文件，以及在我们的文件中使用的接口的方法。

```
// CommonJS
const foo = require('./foo');
module.exports = bar;

// ES Modules
import foo from './foo';
export default bar; 
```

## 我们如何捆绑？

利用从模块系统收集的信息，我们如何将文件链接在一起并生成封装了所有内容的包文件呢？

如果你仔细观察由 [webpack](https://webpack.js.org) 和 [rollup](https://rollupjs.org) 生成的捆绑包，你会注意到这两个最流行的捆绑包采用了完全不同的捆绑方式，在这里我创造了它们，分别是***【web pack 方式】*** 和***【roll up 方式】*** 。

让我们用一个例子来说明这一点:

假设你有三个文件，`circle.js`、`square.js`和`app.js` :

```
// filename: circle.js
const PI = 3.141;
export default function area(radius) {
  return PI * radius * radius;
} 
```

```
// filename: square.js
export default function area(side) {
  return side * side;
} 
```

```
// filename: app.js
import squareArea from './square';
import circleArea from './circle';
console.log('Area of square: ', squareArea(5));
console.log('Area of circle', circleArea(5)); 
```

### “网络打包方式”

“webpack 方式”捆绑包会是什么样子？

```
// filename: webpack-bundle.js
const modules = {
  'circle.js': function(exports, require) {
    const PI = 3.141;
    exports.default = function area(radius) {
      return PI * radius * radius;
    }
  },
  'square.js': function(exports, require) {
    export.default = function area(side) {
      return side * side;
    }
  },
  'app.js': function(exports, require) {
    const squareArea = require('square.js').default;
    const circleArea = require('circle.js').default;
    console.log('Area of square: ', squareArea(5))
    console.log('Area of circle', circleArea(5))
  }
}

webpackStart({
  modules,
  entry: 'app.js'
}); 
```

*为了便于说明，我做了一些小小的修改*

您首先会注意到的是**“模块图”**。它是一个将模块名映射到模块本身的字典，模块由一个函数包装。“模块图”就像一个注册表，通过添加条目可以很容易地注册模块。

其次，**每个模块由一个函数**包装。该函数模拟模块范围，其中模块中声明的所有内容都在其自身范围内。该函数本身被称为“模块工厂函数”。如您所见，它接受几个参数，允许模块导出其接口，以及从其他模块请求。

第三，应用程序通过`webpackStart`启动，这是**一个将所有东西粘合在一起的功能**。函数本身，通常被称为*“运行时”*，是这个包中最重要的部分。它使用“模块图”和入口模块来启动应用程序。

```
// filename: webpack-bundle.js

function webpackStart({ modules, entry }) {
  const moduleRegistry = {};
  const require = moduleName => {
    // if in cache, return the cached version
    if (moduleCache[moduleName]) {
      return moduleCache[moduleName];
    }
    const exports = {};
    // this will prevent infinite "require" loop
    // from circular dependencies
    moduleCache[moduleName] = exports;

    // "require"-ing the module,
    // exported stuff will assigned to "exports"
    modules[moduleName](exports, require);
    return moduleCache[moduleName];
  };

  // start the program
  require(entry);
} 
```

*为了便于说明，我做了一些小小的修改*

`webpackStart`定义了两件事,“要求”函数和模块缓存。“要求”功能与 CommonJS 中的`require`不同。“require”接受模块名，并从模块返回导出的接口，例如:对于`circle.js`，它将是`{ default: function area(radius){ ... } }`。导出的接口缓存在模块缓存中，这样如果我们重复调用同一个模块名的“require”，那么“模块工厂函数”只会执行一次。

定义了“require”之后，启动应用程序将只是“要求”输入模块。

### 【上卷方式】

现在您已经看到了 webpack 捆绑包的样子，让我们来看看“rollup way”捆绑包:

```
// filename: rollup-bundle.js
const PI = 3.141;

function circle$area(radius) {
  return PI * radius * radius;
}

function square$area(side) {
  return side * side;
}

console.log('Area of square: ', square$area(5));
console.log('Area of circle', circle$area(5)); 
```

*为了便于说明，我做了一些小小的修改*

首先，与 webpack 捆绑包相比，rollup 捆绑包的主要区别在于它要小得多。与“webpack 方式”相比，没有**模块映射**。所有的模块都被**【展平】成捆**。没有模块的函数包装。在模块中声明的所有变量/函数现在都在全局范围内声明。

如果在单个模块作用域中声明的所有东西现在都被声明到全局作用域中，*如果两个模块声明了同名的变量/函数会发生什么？*

rollup 将**重命名变量/函数名**，这样就不会发生名称冲突。在我们的例子中，`circle.js`和`square.js`都在模块中声明了`function area(){}`，当绑定时，您会看到两个函数及其用法都被重命名以避免冲突。

> *不用函数包装模块的副作用之一就是`eval`的行为，更深入的解释*见[这里](http://rollupjs.org/guide/en/#avoiding-eval)

其次，**包中模块的顺序很重要**。你可以争辩说`circle$area`和`square$area`可以在`console.log`之后，它仍然会工作，但是`PI`必须在`console.log`之前声明，因为[是时间死区](https://wesbos.com/temporal-dead-zone/)。因此，按照模块的依赖关系对模块进行排序对于“rollup 方式”很重要。

总而言之，“汇总方式”似乎比“网络包方式”更好。通过删除所有的函数，它有一个更小的包和更少的运行时开销。

*“上卷方式”是否有弊端？*

嗯，有时候它在循环依赖的情况下不太好用。让我们来看看这个人为的例子:

```
// filename: shape.js
const circle = require('./circle');

module.exports.PI = 3.141;

console.log(circle(5)); 
```

```
// filename: circle.js
const PI = require('./shape');
const _PI = PI * 1
module.exports = function(radius) {
  return _PI * radius * radius;
} 
```

*为了便于说明，我做了一些小小的修改*

在这个例子中，`shape.js`依赖于`circle.js`，而`circle.js`依赖于`shape.js`。因此，对于 rollup 来说，在输出包中选择哪个模块比另一个模块先出现是没有“正确”答案的。无论是`circle.js`然后`shape.js`还是`shape.js`然后`circle.js`都是合理的。因此，您可能会得到下面的输出包:

```
// filename: rollup-bundle.js
// cirlce.js first
const _PI = PI * 1; // throws ReferenceError: PI is not defined
function circle$Area(radius) {
  return _PI * radius * radius;
}

// shape.js later
const PI = 3.141;
console.log(circle$Area(5)); 
```

你能看出来这是有问题的，对吗？

对此有解决办法吗？一个简短的回答是**没有**。

一个“简单”的解决方法是不使用循环依赖。如果遇到警告，Rollup 会向你抛出警告。

这个例子之所以“有效”,是因为我们的语句可以在模块中立即被求值。如果我们把`_PI`的评价改成懒:

```
// filename: circle.js
const PI = require('./shape');
const _PI = () => PI * 1; // to be lazily evaluated
module.exports = function(radius) {
  return _PI() * radius * radius;
} 
```

模块的顺序现在并不重要:

```
// filename: rollup-bundle.js
// cirlce.js first
const _PI = () => PI * 1;
function circle$Area(radius) {
  return _PI() * radius * radius;
}

// shape.js later
const PI = 3.141;
console.log(circle$Area(5)); // prints 78.525 
```

这是因为在评估`_PI`时，`PI`已经被定义。

## 总结

那么，让我们总结一下目前为止我们学到的东西:

*   *模块捆绑器*帮助我们将多个 JavaScript 模块组合成一个 JavaScript 文件。
*   不同的捆扎机捆绑方式不同，我们已经研究了两种现代捆扎机，`webpack`和`rollup`
*   “网络打包方式”:
    *   使用模块映射
    *   使用函数包装每个模块
    *   有一个运行时代码将模块粘合在一起
*   “上卷方式”:
    *   更扁平、更小的管束
    *   不使用函数包装模块
    *   顺序问题，需要根据依赖性进行排序
    *   循环依赖可能不起作用

## 参考文献

*   [网络包](https://webpack.js.org)
*   [Rollup](http://rollupjs.org/guide/en/)

* * *

这是来自我的博客的转贴。

* * *

如果你喜欢这篇文章，并希望阅读更多类似的文章，请在 Twitter 上关注我