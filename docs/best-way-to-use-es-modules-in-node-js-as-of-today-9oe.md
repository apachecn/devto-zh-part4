# 迄今为止在 Node.js 中使用 ES 模块的最佳方式

> 原文：<https://dev.to/kau/best-way-to-use-es-modules-in-node-js-as-of-today-9oe>

前几天，我开始在 Node.js 中为一些业余爱好项目编写一个基本服务器，并意识到我已经非常习惯于使用 ES 风格的模块导入，Node.js 中缺少它令人厌烦。这篇文章是我随后研究的结果。

### node . js 说你能做什么

在 Node.js 的当前稳定版本(10.16)中，ES 模块处于“实验”状态。因此，要使用它，你必须使用
`--experimental-modules`标志，并且文件应该用扩展名`.mjs`保存。

`$ node --experimental-modules app.mjs`

在 Node 的最新版本(12.6)中，可以保留文件的扩展名为`.js`，但在最近的`package.json`中添加`"type": "module"`。更多信息请点击[这里](https://nodejs.org/dist/latest-v12.x/docs/api/esm.html#esm_ecmascript_modules)

### 这个问题

ES 模块和 CommonJS 模块是互斥的。您不能在一个`.mjs`文件中混合搭配`require`和`import`。这里显而易见的问题是，当你已经有了`node_module`要求的样板文件，所有这些你都必须更新到类似`import * as ...`的东西

例如，如果我想以 ES 模块的方式导入我的 api 处理程序，而让其他的样板文件保持原样，这是行不通的。

```
 var express = require('express')
    var router = express.Router();
    import { GetHandler, PostHandler } from './handlers';

    router.get('/:id?', GetHandler);
    router.post('/', PostHandler);

    module.exports = router 
```

Enter fullscreen mode Exit fullscreen mode

即使你有一个中等规模的项目，你也会花费大量的时间来改变许多文件的需求和文件名，这是不对的。

### 一解

用巴别塔。我们一直都在使用 babel 进行前端项目。但是对于服务器端，我们不必担心开发不同的目标浏览器等。您最有可能使用的是 Node 的一个最新版本，因此只需向下转换到那个版本。但即便如此，我还是觉得巴贝尔对于我们在这里想要达到的目标来说有点过头了。

### 这个用例的最佳解决方案:蔗糖酶( [github](https://github.com/alangpierce/sucrase) )

"蔗糖酶是巴别塔的替代品，允许超快速开发构建."

蔗糖酶并不能满足所有的使用情况。它假设您正在为最新版本的浏览器或 Node.js 进行开发，因此只做 babel 所做工作的一部分，使它自动变得更快。据他们说，它比巴别塔快 20 倍！

这是一篇关于蔗糖酶的文章

[![iriskatastic image](img/56d1625009d69fdc5ea30f851773e11b.png)](/iriskatastic) [## 蔗糖酶是巴别塔的超快速替代品

### iren Korkishko 6 月 27 日 182 分钟阅读

#babel #javascript #typescript #webdev](/iriskatastic/sucrase-is-a-super-fast-alternative-to-babel-4ih0)

要了解更多信息，请访问他们的 github 页面。

因此，对于我们的用例，我们需要做的就是使用应用程序顶部的 sucrase require 挂钩，我们就可以开始了！

```
 // server.js
    require('dotenv').config()
    if (process.env.NODE_ENV === 'development') 
        require('sucrase/register');

    const express = require('express')
    let app = express()
    const port = process.env.PORT;

    /**
     *  OTHER SERVER CODE
     */

    app.listen(port, () => console.log(`Server listening on port ${port}...`)); 
```

Enter fullscreen mode Exit fullscreen mode

##### 警告！不建议在生产中使用 require 挂钩。对于生产，使用蔗糖酶 cli。