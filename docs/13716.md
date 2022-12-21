# 什么是“要求解决”,它是如何工作的？

> 原文：<https://dev.to/stephencweiss/what-is-require-resolve-and-how-does-it-work-1ho4>

为了理解`require.resolve`，节点 API 的一部分，我们必须首先理解它的上下文。也就是说，`require`模块以及它如何适应 Javascript 中模块的导入/导出和 require/module.export 领域。

在许多 Javascript 文件的顶部，我们导入要使用的库(例如，Lodash、React 等。)和/或导出模块的其他本地文件(例如，反应组件或实用函数)。

例如，如果我们想导入要使用的模块，我们可以编写如下代码:

```
import React from 'react'; // a default exported module from a library
import * as _ from 'lodash'; // a name space for all exported modules from a library
import { Link } from 'gatsby'; // a named exported module from a library
import Layout from '../components/Layout'; // a local default exported module 
```

正如我之前在我的导入/导出入门中提到的，这些都需要转换，因为还没有 JS 引擎支持导入(但那是 Babel，Webpack 等。都是为了)。

或者，我们可以这样写:

```
var React = require('react');
var _ = require('lodash')
var { Link } = require('gatsby')
var Layout = require('../components/Layout') 
```

然而，有时我们只是想要一个文件的路径。这种情况在服务器端更为常见。过去，我使用节点的`path`模块使用`path.join(__dirname, "module-i-am-looking-for")`来获取路径。

```
var fs = require('fs');
var path = require("path");

// "__dirname" provides the absolute path to the current module directory.
console.log( fs.readFileSync (path.join( __dirname, "module-i-am-looking-for.js") ) ); 
```

然而，还有一种选择。它是`require.resolve` API。根据节点文档，`require.resolve`“使用内部的`require()`机制来查找模块的位置，但是不加载模块，只返回解析后的文件名。”

```
var fs = require('fs');

// "__dirname" provides the absolute path to the current module directory.
console.log( fs.readFileSync (require.resolve("module-i-am-looking-for.js") ) ); 
```

正如 Ben Nadel 所指出的，这种方法本身有更多的开销，但是从可读性的角度来看，这里有一些重要的优势。

# 资源

*   [JS 模块初级:导出&要求| /*代码注释*/](https://www.stephencharlesweiss.com/2019-02-11/js-modules-primer-export-and-require/)
*   [模块| Node.js v12.6.0 文档](https://nodejs.org/api/modules.html#modules_require_resolve_request_options)
*   [使用 require.resolve()计算 Node.js | Ben Nadel](https://www.bennadel.com/blog/3243-using-require-resolve-to-calculate-module-relative-file-paths-in-node-js.htm) 中相对于模块的文件路径