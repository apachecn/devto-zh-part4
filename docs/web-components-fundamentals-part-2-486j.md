# Web 组件基础，第 2/3 部分

> 原文：<https://dev.to/pdesjardins90/web-components-fundamentals-part-2-486j>

## 概述

在本系列的第一部分中，我们研究了 web 组件背后的四个主要概念中的两个:定制元素和影子 DOM。这太残酷了。如果你们都同意，我认为我们应该看到一些更简单的东西！

剩下的两个概念是 *ES 模块*和 *HTML 模板*。

但是在我们开始之前，让我们使用一些工具让我们的实验更有趣。

### 工具

既然您正在阅读这篇非常高级的 web 开发文章，我假设您的计算机上已经安装了 Node.js。请在你的文件系统上创建一个新文件夹，进入并运行:

```
npm init # just hit all default options
npm install --save-dev es-dev-server
touch index.html
npx es-dev-server --open --node-resolve 
```

Enter fullscreen mode Exit fullscreen mode

这将安装 [es-dev-server](https://www.npmjs.com/package/es-dev-server) ，一个瘦节点包(由 [open-wc](https://open-wc.org) 社区制作)创建一个本地服务器，为我们刚刚在你的浏览器中创建的`index.html`文件提供服务。

现在只需打开您最喜欢的文本编辑器，并在`index.html` :
中添加以下标记

```
<html>
  <head>
    Web Components Experiments
  </head>
  <body>
    <style>
      /* A little optional style for your eyes */
      body {
        background-color: #1C1C1C;
        color: rgba(255, 255, 255, 0.9);
      }
    </style>

    <h1>Hello, world!</h1>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

刷新的话，应该会看到一个大大的“你好，世界！”在您的浏览器中。好了，现在让我们开始吧！

## 我们在哪里

让我们从在这个页面中添加一堆`<my-cool-div>`组件开始！将此标记添加到`<body>` :

```
<style>
  my-cool-div {
    width: 100%;
    height: 200px;
  }
</style>

<my-cool-div>
  <p>Oh yeah</p>
</my-cool-div>

<my-cool-div>
  <p>Drink this Koolaid</p>
</my-cool-div> 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在刷新，您会注意到我们创建的节点没有将它们的内容居中，也没有在上面显示蓝线。那是因为我们忘了在这个浏览器窗口中定义`<my-cool-div>`！如果没有相应的定义，浏览器会将那些未知的标签视为基本的 HTML 节点，如果您愿意，就像`<div>`一样。如果我们稍后定义它们，那么它会将它们升级为自定义元素。我们开始吧！让我们把前面的代码复制到下面的脚本标签中:

```
<script>
  class MyCoolDivElement extends HTMLElement {
    constructor() {
      super()
      this.attachShadow({ mode: 'open' })
      const style = document.createElement('style')
      style.textContent = `
        :host {
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
        }

        span {
          width: 50%;
          height: 4px;
          background-color: blue;
        }
      `

      this.shadowRoot.appendChild(style)

      const span = document.createElement('span')
      this.shadowRoot.appendChild(span)

      const slot = document.createElement('slot')
      this.shadowRoot.appendChild(slot)
    }
  }

  window.customElements.define('my-cool-div', MyCoolDivElement)
</script> 
```

Enter fullscreen mode Exit fullscreen mode

如果您刷新，我们的酷 div 现在应该看起来像预期的。

## ES 模块

> 好吧，不错，但我不想在我做的每个项目中复制粘贴这些代码，更别说在 index.html 了！

嗯朋友，平台有货。它叫做 [ECMAScript 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)。您可以从不同的 JS 文件导入/导出代码。这对于可重用性非常重要，也是 web 组件背后的核心概念之一。让我们看看它是如何工作的:

```
<!-- index.html -->
<html>
  <head>
    Web Components Experiments
  </head>
  <body>
    <!-- ... -->
    <script type="module" src="my-cool-div.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
//my-cool-div.js
export class MyCoolDivElement extends HTMLElement {
  /* ... */
}

window.customElements.define('my-cool-div', MyCoolDivElement) 
```

Enter fullscreen mode Exit fullscreen mode

标准的`<script>`标签和`<script type="module">`标签的主要区别在于，ran 脚本将被封装并只运行一次。这意味着`class MyCoolDivElement`将不再是一个全局变量(yay ),如果我们多次导入该脚本，我们就不会试图在注册表中多次定义它。

> 为什么我们要多次导入它？

例如，如果我们创建另一个 web 组件，在它的影子 dom 中有一个`<my-cool-div>`,我们需要将它作为一个依赖项导入到它的模块中！示例:

```
// my-cool-section.js
import './my-cool-div.js'

class MyCoolSectionElement extends HTMLElement {
  constructor() {
    super()
    this.attachShadow({ mode: 'open' })
    const section = document.createElement('section')
    this.shadowRoot.appendChild(section)
    const myCoolDiv = document.createElement('my-cool-div')
    section.appendChild(myCoolDiv)
  }
}

window.customElements.define('my-cool-section', MyCoolSectionElement) 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果我们想制作另一个 web 组件，它也是一个很酷的 div，但是更酷:

```
// my-cooler-div.js
import { MyCoolDivElement } from './my-cool-div.js'

class MyCoolerDivElement extends MyCoolDivElement {
  constructor() {
    super()
    const coolerStyle = document.createElement('style')
      coolerStyle.textContent = `
        span {
          background-color: white;
        }
      `

      this.shadowRoot.appendChild(coolerStyle)
  }
}

window.customElements.define('my-cooler-div', MyCoolerDivElement) 
```

Enter fullscreen mode Exit fullscreen mode

没错，web 组件是可扩展的！他们毕竟只是阶级！我们现在不会进一步探讨这个概念，但我们会回来的。需要记住的重要一点是，导入 ES 模块意味着它们只运行一次，并且是有作用域的(除非您有意在`window`中产生副作用，就像我们在最后对定制元素注册所做的那样)。

## HTML 模板

好了，现在我们可以定义一个自定义元素，定义它的影子 DOM，并从一个外部文件将其作为一个模块导入。少了什么？没什么。我们拥有构建可重用 web 组件所需的一切！从现在开始，我们加入的所有东西要么是为了提高性能，要么是为了开发人员的体验。

说到这，你注意到建造暗影大教堂有多糟糕了吗？手动创建节点并不能带来最佳的开发体验。让我们试着用 [<模板>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) 对这部分做一点改进。这个元素意味着从标记中定义一个 DOM 结构，就像`<div>`，但是浏览器不会马上在 DOM 中标记它的内容，而是保存它以供重用。这很有用，因为它允许您克隆实例化的模板内容，并稍后在 DOM 中标记克隆，从而获得更好的渲染性能。有了它，我们可以像这样编写我们的组件:

```
// my-cool-div.js

 const template = document.createElement('template')
 template.innerHTML = `
  <style>
    :host {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
    }

    span {
      width: 50%;
      height: 4px;
      background-color: blue;
    }
  </style>

  <span></span>
  <slot></slot>
`

export class MyCoolDivElement extends HTMLElement {
  constructor() {
    super()
    this.attachShadow({ mode: 'open' })
    const templateClone = template.content.cloneNode(true)
    this.shadowRoot.appendChild(templateClone)
  }
}

customElements.define('my-cool-div', MyCoolDivElement) // we can also drop window here for a bit more conciseness 
```

Enter fullscreen mode Exit fullscreen mode

读起来更好一点，对吗？

> 为什么在类外声明模板？

为了性能！克隆一个模板实例比我们过去一个接一个地创建所有的影子 DOM 节点要快得多(特别是当只有一部分需要更新的时候，我们将会看到)。另外，模板创建将只发生一次，因为这个脚本将作为 ES 模块运行！这是如此的方便和高效，以至于它基本上被认为是 web 组件规范的一部分，尽管，正如我们所看到的，对于像`<my-cool-div>`这样非常简单的组件来说，你可以不需要它。

当然，web 组件可以比这复杂得多！封装 DOM 树和一系列样式只是通向有用之路的第一步。在本系列的最后一部分，我们将看到 web 组件如何处理任何类型的数据，并在数据发生变化时做出反应。我们还将利用这个机会更深入地研究 web 组件的生命周期。最后，我们将看看我在第 1 部分中告诉您的 Google 库，它将删除我们刚刚学习的所有低级样板代码，使我们的生活更加轻松！