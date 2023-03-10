# 带有 Pug 模板的 Hyperapp

> 原文：<https://dev.to/johnkazer/hyperapp-with-pug-templates-517e>

我最近完成了一门由詹姆斯·马龙教授的关于 web 应用的函数式 JavaScript 基础的优秀课程。他创建的应用程序框架很好，但是使用 [hyperscript](https://github.com/hyperhype/hyperscript) 将 HTML 定义为函数。我发现 hyperscript 是一种有趣的方法，它确实支持可组合的 UI 组件。但是我真的不喜欢它...

当我在浏览 DEV 时，我注意到[一个来自](https://dev.to/aspittel/functional-programming-in-javascript-done-right-with-hyperapp-570f) [@aspittel](https://dev.to/aspittel) 的帖子，其中提到了 [Hyperapp](https://github.com/jorgebucaran/hyperapp) 。原来这是沿着非常相似的路线构建的，可以选择使用 JSX 和 hyperscript。我想新的 v2 与阿里在 2018 年初检查时相比已经有了很大的变化。

现在，Hyperapp 框架非常棒，有许多我喜欢的特性:

1.  清晰的业务逻辑功能方法
2.  状态驱动用户界面
3.  集中状态，无状态组件(简单的“撤销”选项，非常适合快速可靠地开发中小型应用程序)
4.  调度事件以更新使用 virtualDOM diff 更新 UI 的状态
5.  快速、小巧、简单但足够了

然而，之前我已经使用了 [Pug](https://pugjs.org/api/getting-started.html) 来定义我的 UI 模板。我喜欢保留的查看页面结构的能力，以及逻辑和 UI 更清晰的分离。使用 hyperscript h 函数将 HTML 与业务逻辑结合起来对我来说并不合适(目前？)而且我发现，当页面布局如此抽象时，很难对其进行推理。

也许我最终会回心转意，但也许我不需要...

对我来说幸运的是，这个项目 [pug-vdom](https://github.com/batiste/pug-vdom) (显然)给 pug 带来了一个虚拟的 dom。所以接下来是一个简单的介绍*非常简单的应用程序，我建立这个程序是为了演示 Hyperapp 如何使用 Pug 模板。查看 [Hyperapp 页面](https://github.com/jorgebucaran/hyperapp)以更好地了解它的全部功能，或者尝试他们新的 [Udemy](https://www.udemy.com/hands-on-web-development-with-hyperapp-v2/learn/lecture/14983948) / [Packt](https://www.packtpub.com/gb/application-development/hands-web-development-hyperapp-v2-video) 课程。*

作为项目设置，下面是 package.json .需要注意的关键事项是启动脚本和 pug/pug-vdom 依赖关系(并且需要 [Node.js](https://nodejs.org/en/) 版本 6.4 [或以上](https://node.green/))。

```
{  "name":  "hyperapp-pug",  "version":  "1.0.1",  "description":  "An instance of hyperapp which uses pug and pug-vdom rather than the default h functions",  "main":  "main.js",  "scripts":  {  "start":  "node ./compilePug.js && parcel ./src/index.html"  },  "author":  "John Kazer",  "license":  "ISC",  "devDependencies":  {  "parcel":  "1.12.4"  },  "dependencies":  {  "hyperapp":  "2.0.12",  "pug":  "2.0.4",  "pug-vdom":  "1.1.2"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这里是基本的项目文件结构

```
\dist (parcel output dir)
\src
  app.pug.js (compiled pug template)
  index.html
  main.js
  pug-to-view.js
  pug-vdom.js (added as a local copy to handle Hyperapp approach to textNodes)
\views
  app.pug
compilePug.js (see the start script)
package.json 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。

*注意:*有一个[为 pug-vdom](https://github.com/batiste/pug-vdom/pull/52) 准备的 PR 包含了这个调整，所以也许将来本地副本可以消失。

可以想象，在运行“npm install”之后，使用“npm run start”脚本运行一个编译过程，将 Pug 视图转换成一个. js 文件。编译由 compilePug.js 定义。编译后的文件包含在 main.js 的`require('./app.pug.js')`中，并提供 Hyperapp 显示内容所需的虚拟 DOM 节点。然后[包](https://parceljs.org/)运行，更新\src 中的其他文件，并启动\dist\index.html 的浏览器实例

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    Hyperapp demo
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body>
    <div id='app'></div>
    <script src="./main.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

编译过程非常简单——一个模板和目的地列表(在本例中只有一个):

```
// compilePug.js
const vDom = require('pug-vdom')
vDom.generateFile('./views/app.pug', './src/app.pug.js', './views') 
```

Enter fullscreen mode Exit fullscreen mode

它编译了我的简单 Pug 模板:

```
// Need a root div to grab as the start of the view
div
    // receives the variables and functions from the supplied state object
    - var greeting = "Hello " + greet
    p(style={color: "red"}) #{greeting}
    input(size="60" placeholder=placeholder onchange=handler.updateMe)
    button(id='clickMe' onclick=handler.clickMe) Click Me
    p #{userText} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们快速浏览一下定义这个应用程序的 main.js】

```
// main.js
import { app, h, text } from 'hyperapp'
import { pugToView } from "./pug-to-view"

const view = pugToView(h, text)

// event handlers
const clickMe = (state, event) => ({
    ...state,
    userText: state.value
})
const updateMe = (state, event) => ({
    ...state,
    value: event.target.value
})

const initialState = {
    greet: 'friends',
    placeholder: 'Write something here first, hit \<enter\> then click the button',
    value: '',
    userText: '',
    handler: {
        clickMe,
        updateMe
    }
}

const node = document.getElementById('app')

app({
    init: initialState,
    view: view,
    node: node
}) 
```

Enter fullscreen mode Exit fullscreen mode

帮助器函数 pugToView 从这里来

```
 import 'pug-vdom/runtime' // runtime library is required and puts 'pugVDOMRuntime' into the global scope
const render = require('./app.pug.js')
export const pugToView = (h, text) => state =>
  render(
    state,
    (name, props, children) => h(name, props.attributes, children),
    text
  )[0] // grabs the root 'div' element whilst adjusting the way pug-vdom deals with props compared to hyperapp 
```

Enter fullscreen mode Exit fullscreen mode

main.js 文件使用 Hyperapp 提供的三个 API 函数(没错，就三个！)、`h`、`text`和`app`。这些函数提供了状态管理、虚拟 DOM、diffing 和状态改变时的 DOM 修补。

这是一个静态页面，所以只有简单的初始状态；一些初始值以及与按钮和输入元素相关联的事件处理程序。

我们将通过使用`content`函数提供由编译的 Pug 模板定义的`view`,在选择的`node`注入应用程序。

`app`函数将所有这些放在一起，Hyperapp 负责剩下的部分。简单的声明性、功能性逻辑与熟悉的模板系统相结合！

在这里找到回购[。](https://github.com/johnkazer/hyperapp-pug)