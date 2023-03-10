# 强大的 Javascript 中间件模式实现，将中间件应用于任何对象。

> 原文：<https://dev.to/unbug/powerful-javascript-middleware-pattern-implementation-apply-middleweares-to-any-object-17lj>

# js-中间件

强大的 Javascript 中间件模式实现，将中间件应用于任何对象。使代码像 ReduxJS 和 ExpressJS 一样可扩展和可维护的无痛解决方案。

## 链接

*   [项目概述](https://unbug.github.io/js-middleware/)
*   [文档](https://unbug.github.io/js-middleware/docs/html/)
*   [GitHub 回购](https://github.com/unbug/js-middleware)

# 概述

中间件功能是能够访问目标功能及其参数、
以及目标对象和目标功能周期中的下一个中间件功能的功能。
下一个中间件功能通常由名为 next 的变量表示。

中间件功能可以执行以下任务:

*   执行任何代码。
*   对函数的参数进行更改。
*   结束目标函数。
*   调用堆栈中的下一个中间件。

如果当前中间件函数没有结束目标函数循环，
它必须调用 next()将控制传递给下一个中间件函数。否则，
目标函数将被挂起。

# 开始使用

1.  **窗口。通过在 HTML 中包含 [`dist/middleware.min.js`](https://github.com/unbug/js-middleware/tree/master/dist) 文件，浏览器可以使用 MiddlewareManager** 。

```
 <script src="middleware.min.js"></script> 
```

1.  或者安装软件包

```
npm install --save js-middleware 
```

并将其导入您的文件

```
import {MiddlewareManager} from 'js-middleware'; 
```

# 用途

## 基本

我们定义一个人类。

```
// the target object
class Person {
  // the target function
  walk(step) {
    this.step = step;
  }

  speak(word) {
    this.word = word;
  }
 } 
```

然后我们定义一个中间件函数来打印日志。

```
 // middleware for walk function
 const logger = target => next => (...args) => {
    console.log(`walk start, steps: ${args[0]}.`);
    const result = next(...args);
    console.log(`walk end.`);
    return result;
  } 
```

现在，我们将日志函数作为中间件应用于一个人实例。

```
 // apply middleware to target object
 const p = new Person();
 const middlewareManager = new MiddlewareManager(p);
 middlewareManager.use('walk', logger);
 p.walk(3); 
```

每当一个 Person 实例调用它的 walk 方法时，我们都会看到来自 looger 中间件的日志。

## 中间件对象

我们还可以将中间件对象应用到目标对象。中间件对象是包含与目标对象的函数名相同的函数名的对象。
以“_”开头或结尾的函数名将无法应用中间件。

```
const PersonMiddleware = {
  walk: target => next => step => {
    console.log(`walk start, steps: step.`);
    const result = next(step);
    console.log(`walk end.`);
    return result;
  },
  speak: target => next => word => {
    word = 'this is a middleware trying to say: ' + word;
    return next(word);
  }
}

 // apply middleware to target object
 const p = new Person();
 const middlewareManager = new MiddlewareManager(p);
 middlewareManager.use(PersonMiddleware);
 p.walk(3);
 p.speak('hi'); 
```

## 中间件方法

在类中，以“_”开头或结尾的函数名不能作为中间件使用。
或者我们可以使用`middlewareMethods`在一个类中为中间件目标定义函数名。

```
class PersonMiddleware {
  constructor() {
    /**
     * Or Define function names for middleweare target.
     * @type {Array}
     */
    this.middlewareMethods = ['walk', 'speak'];
  }
  // Function's name start or end with "_" will not be able to apply as middleware.
  _getPrefix() {
   return 'Middleware log: ';
  }
  log(text) {
    console.log('Middleware log: ' + text);
  }
  walk(target) {
    return next => step => {
      this.log(`walk start, steps: step.`);
      const result = next(step);
      this.log(`walk end.`);
      return result;
    }
  }
  speak(target) {
    return next => word => {
      this.log('this is a middleware tring to say: ' + word);
      return next(word);
    }
  }
}

 // apply middleware to target object
 const p = new Person();
 const middlewareManager = new MiddlewareManager(p);
 middlewareManager.use(new PersonMiddleware())
 p.walk(3);
 p.speak('hi'); 
```

# 原料药

### [T1。使用(方法名称，...中间件)](#usemethodname-middlewares)

将中间件功能应用(注册)到目标功能或应用(注册)中间件对象。
如果第一个参数是中间件对象，其余参数必须是中间件对象。

*   **{string|object}** 目标函数名的 methodName 字符串，中间件对象的 object。
*   **{...function}** 中间件要应用的中间件链。
*   返回 **{object}** 这个

# 打造

1.  运行`npm install`来安装需求。

2.  运行`gulp`构建库，生成`dist/middleware.js`作为核心脚本，监视文件变化，
    启动 HTTP 服务器进行调试。

```
 Usage
    gulp [TASK] [OPTIONS...]

  Available tasks
    build       Builds the library.
    clean       Cleans files.
    clean:dist  Cleans dist files.
    clean:docs  Cleans docs files.
    default    
    docs        Builds documentation.
    docs:html   Builds HTML documentation.
    docs:md     Builds markdown documentation.
    help        Display this help text.
    lint        Lint JS files.
    mini        Minify the library.
    server      Starts a HTTP server for debug.
    test        Run test cases.
    watch       Watches for changes in files, re-lint, re-build & re-docs. 
```

1.  运行`gulp docs`构建文档。用`docs/API.md`查看 markdown 文档，或者运行`gulp server`启动 HTTP 服务器，用[localhost:3000/docs/HTML/](https://dev.tolocalhost:3000/docs/html/)查看 HTML 文档。

# 路线图&建功立业

*   支持正则表达式匹配方法名，将当前方法名作为参数传递给当前中间件。
*   **once(方法名，...中间件)**应用中间件只运行一次。
*   能够**取消使用**中间件。