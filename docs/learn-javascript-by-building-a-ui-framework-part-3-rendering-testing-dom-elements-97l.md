# 通过构建 UI 框架学习 JavaScript:第 3 部分——渲染和测试 DOM 元素

> 原文：<https://dev.to/carlmungazi/learn-javascript-by-building-a-ui-framework-part-3-rendering-testing-dom-elements-97l>

本文是深入研究 JavaScript 系列文章的第三篇。可以查看往期文章[这里](https://dev.to/carlmungazi/learning-javascript-by-building-a-ui-framework-from-scratch-1767)这里[这里](https://dev.to/carlmungazi/learn-js-by-building-a-ui-framework-part-2-testing-3pff)。

本系列并没有全面涵盖每一个 JavaScript 特性。相反，当功能出现在各种问题的解决方案中时，它们会被包含在内。另外，每篇文章都是基于教程和其他开发者开发的开源库，所以和你一样，我也从每篇文章中学到了新的东西。

* * *

上次我们为我们的框架写了一个测试库。在今天的帖子中，我们将为这个框架添加更多的功能

```
const aprender = require('../src/aprender');

const button = aprender.createElement('button', { children: ['Click Me!'] });
const component = aprender.createElement(
  'div', 
  {
    attrs: { id: 'app'},
    children: [
      'Hello, world!',
      button
    ]
  },
);

const app = aprender.render(component);

aprender.mount(app, document.getElementById('app')); 
```

变成这样:

[![](img/8a17b2dc6ef28161a96144d91f4a3046.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jThTweby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6bny85qhusdrptfhqne4.png)

我们还将进行新的测试:

```
let element;
let $root;
let app;

beforeAll(() => {
  element = createElement('div', {
    children: [
      createElement('h1', { children: ['Hello, World'] }),
      createElement('button', { children: ['Click Me!'] }),
    ]
  });

  createMockDom();

  $root = document.createElement("div");
  $root.setAttribute('id', 'root');
  document.body.appendChild($root);

  app = render(element);
});

check('it creates DOM elements', () => {
  assert.isDomElement( app );
});

check('it mounts DOM elements', () => {
  mount(app, document.getElementById('root'));

  assert.isMounted(app, $root);
}); 
```

### 从 JavaScript 对象到 DOM 元素

让我们首先提醒自己当前的文件结构:

```
- aprender
  - src
    - createElement.js
  - tests
    - index.js
- examinar
  - node_modules
    - colors
  - package.json
  - package-lock.json
  - src
    - assertions
      - deep-equal.js
      - index.js
    - index.js 
```

Aprender 是我们框架的名字，它包含了`createElement`函数及其相关的测试。Examinar 是我们的测试框架，它有一个包含`colors`包的节点模块文件夹。在断言文件夹中，对象等式实用函数`deep-equal`位于它自己的文件中。索引文件包含断言`isDeeplyEqual`和`throws`。我们要做的第一件事是在`aprender`中创建一个名为`demo`的文件夹，这样我们就可以构建将`Hello, World`打印到浏览器的示例应用程序。在`demo`文件夹中，我们创建一个`index.html`和`index.js`文件:

```
<html>
  <head>
    Hello, World
  </head>
  <body>
    <div id="app"></div>
    <script src="./index.js"></script>
  </body>
</html> 
```

```
const aprender = require('../src/aprender');

const button = aprender.createElement('button', { children: ['Click Me!'] });
const component = aprender.createElement(
  'div', 
  {
    attrs: { id: 'app'},
    children: [
      'Hello, world!',
      button
    ]
  },
);

const app = aprender.render(component);

aprender.mount(app, document.getElementById('app')); 
```

`index.js`文件包含两个新方法:`render`和`mount`。`render`将分配给`component`的 JavaScript 对象转换成 DOM 元素，而`mount`将新创建的元素附加到`<div id="app"></div>`中。`render`的代码是:

```
function renderElement({ type, attrs, children }) {
  const $el = document.createElement(type);

  for (const [attribute, value] of Object.entries(attrs)) {
    $el.setAttribute(attribute, value);
  }
  for (const child of children) {
    $el.appendChild(render(child));
  }

  return $el;
};

function render(vNode) {
  if (typeof vNode === 'string') {
    return document.createTextNode(vNode);
  }

  return renderElement(vNode);
}; 
```

由于可以通过`renderElement`递归调用`render`，它需要首先检查它是否正在处理一个字符串。如果是，我们创建一个文本节点并返回它。否则，我们返回用虚拟 dom 对象调用`renderElement`的结果。`renderElement`然后遍历`attrs`对象，设置它找到的任何属性，然后处理`children`数组中出现的任何子元素。`mount`函数更简单:

```
function mount($app, $root) {
  return $root.appendChild($app);
} 
```

例如，如果我们正在构建 React，`mount`的等价物将是 [`ReactDOM.render`](https://reactjs.org/docs/react-dom.html#render) 。像`ReactDOM.render`一样，`mount`将我们想要添加到 DOM 中的元素作为第一个参数，然后将它附加到指定为第二个参数的元素上。当然，React 的[比那多得多](https://dev.to/carlmungazi/a-journey-through-reactdom-render-302c)，但是在`createElement`、`render`和`mount`之间，你有大多数 UI 框架用来创建 DOM 元素的基本工作流。不同之处在于它们在管理事件、跟踪状态和执行更新之间所做的所有事情。

Aprender 不是一个用于生产的框架，但是如果它是，我们需要确保它正确地创建和附加 DOM 元素。这个测试将发生在 node 中，node 没有 DOM，那么我们能做什么呢？我们可以:

*   使用 [`jsdom`](https://github.com/jsdom/jsdom) 。Jest 和 Enzyme 这两个流行的测试工具使用了它
*   存根 DOM 对象，如这里的[所示](https://codeutopia.net/blog/2016/05/23/sinon-js-quick-tip-how-to-stubmock-complex-objects-such-as-dom-objects/)
*   使用[无头浏览器](https://github.com/dhamaniasad/HeadlessBrowsers)

所有这些都是很好的选择，但是对我们来说，使用 jsdom 或无头浏览器是多余的。Stubbing dom 对象并不能完全满足我们的需求，因为我们想要模拟创建一个虚拟 DOM 对象，将其转换为 DOM 元素，然后将其添加到 DOM 树中的过程。幸运的是，Mithril.js 框架已经解决了这个问题。维护者在 node 中模仿 DOM 作为他们测试套件的一部分。因此，我们将根据它们的实现来建模我们自己的 DOM，但会根据我们的需要进行调整。这些需求体现在以下测试中:

```
group('aprender', () => {
  let element;
  let $root;
  let app;

  beforeAll(() => {
    element = createElement('div', {
      children: [
        createElement('h1', { children: ['Hello, World'] }),
        createElement('button', { children: ['Click Me!'] }),
      ]
    });

    createMockDom();

    $root = document.createElement("div");
    $root.setAttribute('id', 'root');
    document.body.appendChild($root);

    app = render(element);
  });

  check('it creates DOM elements', () => {
    assert.isDomElement(app);
  });

  check('it mounts DOM elements', () => {
    mount(app, document.getElementById('root'));

    assert.isMounted(app, $root);
  });
}); 
```

`beforeAll`是一个钩子，它把一个函数作为它的参数，然后调用它。我们提供的函数创建我们的虚拟 dom 对象，然后通过`createMockDom()`初始化我们的模拟 DOM。

### 什么是 DOM？

根据 MDN Web Docs，*“文档对象模型(DOM)是组成 Web 上文档的结构和内容的对象的数据表示*。我们的新测试要求我们需要`createElement`、`appendChild`、`setAttribute`、`createTextNode`和`getElementById`方法，所以我们的模拟 DOM 对象的框架如下所示:

```
const document = {
  createElement() {
    appendChild,
    setAttribute
  },
  createTextNode() {},
  getElementById() {}
}

function appendChild() {}
function setAttribute() {} 
```

我们将从充实`createElement`方法开始:

```
createElement(tag) {
  return {
    nodeType: 1,
    nodeName: tag.toUpperCase(),
    parentNode: null,
    childNodes: [],
    appendChild,
    setAttribute,
    attributes: {},
    $$dom: true
  }
} 
```

它返回一个表示 DOM 元素的对象。真正的 DOM 元素包含的属性比上面列出的更多，但是我们只实现了我们需要的那些。属性`$$dom`是我们自己创造的，当我们讨论新的测试断言`isMounted`和`isDomElement`时，我们将会看到为什么我们需要它。接下来我们将添加设置属性的功能:

```
function setAttribute(name, value) {
  this.attributes[name] = value;
} 
```

追加孩子:

```
function appendChild(child) {
  let ancestor = this;

  if (ancestor === child) throw new Error("Child element cannot be equal to parent element");
  if (child.nodeType == null) throw new Error("The child is not a DOM element");

  const index = this.childNodes.indexOf(child);
  if (index > -1 ) this.childNodes.splice(index, 1);

  this.childNodes.push(child);
} 
```

这个方法最有趣的部分是，如果子元素已经存在于`childNodes`数组中，我们将它移除并重新插入到数组的末尾。如果在目标元素上添加一个已经存在的子元素，这种行为会模拟浏览器中发生的情况。

接下来我们继续讨论`createTextNode`方法:

```
createTextNode(text) {
  return {
    nodeType: 3,
    nodeName: "#text",
    parentNode: null,
    data: text
  }
} 
```

有一个有趣的[堆栈溢出](https://stackoverflow.com/questions/12286975/when-working-with-text-nodes-should-i-use-the-data-nodevalue-textcontent)答案，解释了我们正在使用的`data`属性和文本节点上存在的其他属性之间的区别，这些属性也可以保存文本值。

在`createTextNode`之后，我们来到`getElementById`函数。关于不同的浏览器是如何实现这种特殊方法的，没有明确的答案，但是通过阅读 HTML 规范，我们可以看到遍历 DOM 树是一个可行的选择。我们的 DOM 树不够大，不需要遍历，所以我们将选择更简单的选项，在我们的`document`对象上创建一个名为`_elementIds`的新属性，并为它分配一个空对象。我们将在每次设置 id 时在`setAttribute`方法中填充这个对象:

```
function setAttribute(name, value) {
  this.attributes[name] = value;

  if (name === 'id') {
    if (document._elementIds[value]) {
      throw new Error(`${value} is already the id of an existing element`);
    }
    document._elementIds[value] = this;
  }
} 
```

当被调用时，`getElementById`将返回元素，如果它的 id 存在于`_elementIds`中。

```
getElementById(id) {
  if (document._elementIds[id]) {
    return document._elementIds[id]
  }

  return null;
} 
```

### 测试 DOM

我们的第一个新断言`isDomElement`试图回答以下问题- **如何判断某个东西是否是 DOM 元素？**答案不[直截了当](https://stackoverflow.com/questions/384286/javascript-isdom-how-do-you-check-if-a-javascript-object-is-a-dom-object)。我们可以尝试从 React [如何为 React 元素添加](https://overreacted.io/why-do-react-elements-have-typeof-property/)属性来帮助区分它们和其他不是由 React 创建的元素中获得灵感。我们将通过在我们的`document`对象上创建一个名为`$$dom`的属性并给它赋值`true`来实现这一点。然后我们写`isDomElement` :

```
isDomElement(element) {
  if (element.hasOwnProperty("$$dom") && element.$$dom) return true;

  throw new Error('The supplied element is not a DOM element')
} 
```

我们对`$$dom`和`_elementIds`的实现并不是向我们的`document`对象添加有效元数据的最佳方式。首先，我们可以使用[符号](https://www.keithcirkel.co.uk/metaprogramming-in-es6-symbols/)来确保这些属性不会通过迭代方法显示出来，例如`Object.keys`或`for..of`。但即使这样，仍然可以通过 [`Reflect.ownKeys`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys) 或 [`Object.getOwnPropertySymbols`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols) 方法找到符号，因此解决方案并不完美。对于`getElementById`,我们可以遍历 DOM 树并以这种方式找到元素。谢天谢地，我们在这个阶段的需求相对简单。

第二个断言`isMounted`，测试调用`mount`方法的结果。如前所述，`mount`扮演着与`ReactDOM.render`相似的角色，所以我们需要做的就是检查我们指定的 DOM 元素是我们在`index.html`文件中创建的`<div id="app"></div>`元素的唯一子元素。

```
isMounted(element, parentElement) {
  if (parentElement.childNodes.length > 1) throw new Error('The root element has more than one child');

  if (parentElement.childNodes[0] === element) return true;

  throw new Error('The supplied element has not been mounted');
} 
```

所有新的测试都基于测试环境中存在一个类似 DOM 的结构。这由`createMockDom`方法处理，该方法首先创建一个[根元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/html)，它应该存在于每个非空的 HTML 文档中。然后它添加一个`head`和`body`元素作为子元素。严格地说，`html`元素也应该存在于`document`对象的`childNodes`数组中，但是我们可以在实现中跳过这一步。然后，我们通过将我们的`document`对象添加到节点`global`变量来完成。

```
function createMockDom() {
  document.documentElement = document.createElement("html");
  document.documentElement.appendChild(document.createElement("head"));
  document.body = document.createElement("body");
  document.documentElement.appendChild(document.body);

  global.document = document;
} 
```

### 总结

我们现在有了一个创建和呈现 DOM 元素的框架。还是我们？我们的测试通过了，但是我们如何在浏览器中查看我们的应用程序呢？我们将在下一篇文章中通过构建一个模块捆绑器和设置我们的开发环境来讨论这个问题。与此同时，您可以在这里探索我们到目前为止编写的代码[。](https://github.com/CarlMungazi/aprender-js)

#### 资源

*   [创建虚拟 DOM 教程](https://dev.to/ycmjason/building-a-simple-virtual-dom-from-scratch-3d05)
*   [秘银的 DOM 实现](https://github.com/MithrilJS/mithril.js/blob/next/test-utils/domMock.js)