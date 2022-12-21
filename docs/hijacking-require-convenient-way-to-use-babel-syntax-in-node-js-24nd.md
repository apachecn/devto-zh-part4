# 在 Node 中使用 Babel 语法的便捷方式。射流研究…

> 原文：<https://dev.to/nesterow/hijacking-require-convenient-way-to-use-babel-syntax-in-node-js-24nd>

当你想在服务器端使用最新的 javascript 语法时，通常你会有两种选择:[巴别塔节点](https://babeljs.io/docs/en/babel-node)或 [-实验模块标志](https://nodejs.org/api/esm.html)。
在这篇文章中，我想分享另一种在服务器应用中使用巴别塔的便捷方法，通过一个叫做 [Pirates](https://github.com/ariporad/pirates) 的小库

## 劫持要求

让你通过一个简单的 API 为任何文件类型创建你自己的模块加载器。在其他很酷的事情中，它可以用来预编译用最新的 es 特性编写的 javascript 文件。

考虑下面的例子:

```
const {addHook} = require('pirates');

function matcher(filename) {
  // Here, you can inspect the filename to determine if it should be hooked or not. Just return a truthy/falsey. Files in node_modules are automatically ignored, 
  // TODO: Implement your logic here
  return true;
}

const unregister = addHook(
  (code, filename) => code.replace('@@foo', 'console.log(\'foo\');'), 
  { exts: ['.js'], matcher }
);

// And later, if you want to un-hook require, you can just do:
// unregister(); 
```

Enter fullscreen mode Exit fullscreen mode

`addHook()`接受两种说法:1。解析器回调 2。带有选项的对象。

## 设置项目

让我们用 Pirates 创建一个示例 ExpressJS 项目。

#### 1。安装依赖项:

```
mkdir pirates-test && cd pirates-test
npm init
npm i pirates @babel/preset-env @babel/core express 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。添加巴别塔钩

创建一个名为`nodeHooks.js`的文件，内容如下:

```
 const {addHook} = require('pirates')
const {transform} = require('@babel/core')

module.exports = (options) => addHook(
  function(source, filename) {
    return transform(source, {
      presets: [
        [
          '@babel/preset-env',
          {
            modules: 'cjs',
            targets: {
              node: process.versions.node
            }
          }
        ]
      ]
    }).code
  },
  {
    exts: ['.js'],
    ignoreNodeModules: true,
    ...options
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，挂钩功能与`.babelrc`配置没有太大的不同。

#### 3。为应用程序创建一个入口点

在应用程序中使用 Babel 之前，我们需要注册这个钩子。正确的做法是为我们的应用程序创建一个入口点，在运行服务器之前加载所有配置并注册钩子。

创建包含以下内容的`index.js`:

```
const registerHooks = require('./nodeHooks')
registerHooks()

// load some configs synchronously 
// then run the server

require('./server') 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。用 Babel 编写应用程序

创建包含以下内容的`server.js`:

```
import express from 'express'

const app = express()

app.get('/', (req, res) => {
    res.send('Hello world!')
})

app.listen(8080)

export default app 
```

Enter fullscreen mode Exit fullscreen mode

现在整个应用程序都可以用最新的 ES 特性来编写了。

#### 5。使用 nodemon

使用`nodemon`或您最喜欢的流程管理器，无需额外步骤:

```
nodemon index.js 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样！

[完整代码](https://github.com/nesterow/pirates-examle)