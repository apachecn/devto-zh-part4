# Web 组件基础，第 1/3 部分

> 原文：<https://dev.to/pdesjardins90/web-components-fundamentals-part-1-2dm7>

^这是我们的小罗茜:)

## 概述

如果你是 web 开发的新手，你可能已经注意到入门是多么复杂。如果你是一个老家伙，当你要抛弃一个你最终精通的框架时，你可能至少有一次在键盘上哭泣。在这样一个不断增加的框架和库的生态系统中，有两件事是肯定的:

*   无论你选择什么样的框架或库开始，它在某个时候都会过时。

*   在那个时候，你唯一能做的就是了解基本面。我说的是普通的 HTML、CSS、JavaScript、网络浏览器的工作方式和计算机科学基础。

每当 web 开发领域发生这样的转变时，有一件事情是必须去厕所的，那就是组件模型。每个框架都有自己特殊的构建可重用 UI 组件的方式，所以所有花在学习它如何工作的时间都随着框架的其他部分而消失了。

嗯，不会再有了。输入标准 web 组件。

是的，浏览器制造商终于赶上了现代化，并包括了一种构建这种组件的标准方法，这意味着现在有一种方法可以一次编写这些组件，并让它们伴随你直到互联网的终结！我不是说你永远不会编写特定于框架的组件，但是希望它们会成为例外。

下面，我将解释本地 web 组件背后的概念，以及如何创建它们，我将向您展示一个简洁的库，它目前简化了如何创建它们。毫无疑问，这个库会在某个时候消亡(特别是因为它是由 Google 创建的)，但是当这种情况发生时，您将保持对底层概念的理解:)

### 警告

我们将在这个页面中手动创建 DOM 元素，这很乏味，但在试图理解这些概念时很酷。在这篇文章的下一部分，我们将使用标记和更多有趣的工具，敬请关注。

## 自定义元素

你知道 [< div >](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div) 吧？这是一个标准的 HTML 标签。当浏览器读取一个 HTML 文件并看到一个`<div>`时，它会创建一个[HTML dive element](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDivElement)并在 HTML 文件指定的 DOM 树中标记它。你不需要定义它，因为它已经是平台的一部分，你也不需要自己实例化它，你只需要在标记中指定你想要它的位置，浏览器会处理剩下的事情，酷吧？还有一大堆你知道的标签，`<a>`，`<p>`，`<img>`等等。他们都以同样的方式工作。

[自定义元素](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)是你为自己的目的创建标签的一种方式。您可以创建一个名为`<my-cool-div>`的标签，浏览器会以与标准`<div>`相同的方式在 DOM 中识别和标记该标签。这个酷酷的 div 可以做任何事情。例如，它可以只是一个普通的 div，但是它可以水平和垂直居中放置内容(想象一下再也不用在 stackoverflow 上搜索它了！).它看起来会像这样:

```
 <my-cool-div>This text is centered</my-cool-div> 
```

Enter fullscreen mode Exit fullscreen mode

> 太好了！你是怎么做到的？

有几个步骤，第一步是:

```
class MyCoolDivElement extends HTMLElement {}
window.customElements.define('my-cool-div', MyCoolDivElement) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码做两件事:

*   它创建了一个扩展 HTMLElement 的`MyCoolDivElement`类，这是所有定制元素的本地基类。
*   它在浏览器中注册了`<my-cool-div>`标签。这意味着从现在开始，HTML 标记中出现的任何`<my-cool-div>`都会使浏览器实例化一个`MyCoolDivElement`，并在 DOM 中标记它。

试试看！打开浏览器的控制台，写下这两行。然后这样做:

```
const coolDiv = document.createElement('my-cool-div')
document.body.appendChild(coolDiv) 
```

Enter fullscreen mode Exit fullscreen mode

如果您检查这个页面的 DOM，在主体的底部您会发现一个类型为`<my-cool-div></my-cool-div>`的 DOM 节点。

惊叹吧！

> 但是等等，你之前说的居中代码在哪里？

我们必须在所谓的 web 组件的**影子 DOM** 中定义它！

## 阴影 DOM

这是最复杂的概念，请多包涵。

### 欢迎来到黑暗面

如果你知道`<div>`，你可能也知道`<button>`。试着在本页底部添加一个:

```
const someButton = document.createElement('button')
someButton.textContent = 'Press me'
document.body.appendChild(someButton) 
```

Enter fullscreen mode Exit fullscreen mode

有没有想过为什么默认按钮这么丑？那种风格从何而来？为什么不同的浏览器会有所不同？

答案就在[影子王国](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM)里。它是一个隐藏的子树，您可以在其中添加样式和其他节点到您的自定义元素中，这些元素对外界是不可见的。换言之:**它封装了 CSS 和一个 DOM 子树**。

对于我们的按钮示例，这意味着浏览器开发者在实现`<button>`规范时，决定向按钮元素添加一个难看的默认`<style>`。这种风格被定义在`HTMLButtonElement`的阴影中，不会从那里泄露出去。它可能包括其他节点，如几个`<div>`或`<p>`，我们不知道也不需要知道，我们只是`<button>`的消费者。

我们将做完全相同的事情，默认情况下将`<my-cool-div>`置中。

### 黑暗面的力量

我们已经看到了如何定义我们的自定义元素，让我们在此基础上添加我们需要的样式的阴影根:

```
class MyCoolDivElement extends HTMLElement {
  constructor() {
    super()
    this.attachShadow({ mode: 'open' })
    const style = document.createElement('style')
    style.textContent = `
      :host {
        display: flex;
        justify-content: center;
        align-items: center;
      }
    `

    this.shadowRoot.appendChild(style)
    const slot = document.createElement('slot')
    this.shadowRoot.appendChild(slot)
  }
}

window.customElements.define('my-cool-div', MyCoolDivElement) 
```

Enter fullscreen mode Exit fullscreen mode

要在您的浏览器中试用它，像之前一样在控制台中复制它，然后执行:

```
const myCoolDiv = document.createElement('my-cool-div')
myCoolDiv.textContent = 'This text is centered'
myCoolDiv.style = 'width: 100%; height: 200px;'
document.body.appendChild(myCoolDiv) 
```

Enter fullscreen mode Exit fullscreen mode

在这一页的底部，你应该看到文本像我们希望的那样很好地居中！

好了，现在让我们回退一点，解释一下我们添加到`MyCoolDivElement`定义中的所有新内容:

#### 构造函数

```
class MyCoolDivElement extends HTMLElement {
  constructor() {
    super()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是标准的 javascript，我们只是覆盖了类的默认构造函数，并用`super()`调用其父类的(`HTMLElement`)。每当浏览器在一些标记中读取到一个`<my-cool-div>`时，或者当我们手动调用`document.createElement('my-cool-div')`时，就会调用这个函数。

#### 创建阴影根

```
this.attachShadow({ mode: 'open' }) 
```

Enter fullscreen mode Exit fullscreen mode

这就创建了影子根，我们可以在这里封装样式。你可以问关于`mode: open`的问题，但是这真的超出了这篇文章的范围。

#### 添加样式

```
const style = document.createElement('style')
style.textContent = `
  :host {
    display: flex;
    justify-content: center;
    align-items: center;
  }
`

this.shadowRoot.appendChild(style) 
```

Enter fullscreen mode Exit fullscreen mode

这将创建样式节点并将其添加到阴影根中。注意 CSS `:host`选择器:这是特定于阴影根样式的，它针对的是阴影根本身。更多信息请见下文。

#### 添加插槽

```
const slot = document.createElement('slot')
this.shadowRoot.appendChild(slot) 
```

Enter fullscreen mode Exit fullscreen mode

这是最难搞清楚的事情。这在影根中增加了一种“洞”。传递给元素的内容将放在这里，在我们的例子中是文本`"I am centered"`。因为这个插槽是我们的影子根的子插槽，所以它将根据我们的影子根 flexbox 属性居中！你可能会想象成这样:

```
 <shadow-root> <!-- <my-cool-div> -->
    <slot></slot> <!-- I am centered-->
  </shadow-root> <!-- </my-cool-div> --> 
```

Enter fullscreen mode Exit fullscreen mode

给你！

### 一个影根把他们都带来，在黑暗中把他们捆绑起来

现在想象一下，我们希望每一个居中的东西上面都有一条蓝色的粗线(因为设计师是这么说的)。我们可以四处走走，做些类似的事情

```
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
```

Enter fullscreen mode Exit fullscreen mode

> 等等，用一般的 CSS 选择器那样真的不好用`span {}`！会影响页面中所有的`<span>`！

如果这个样式表是在影子根中定义的，就不会了！那些样式被封装在影子根中，还记得吗？他们不会到处漏雨！试着在一个`<my-cool-div>`旁边加一个`<span>`，你会发现它不是蓝色或者别的什么:

```
const myCoolDiv = document.createElement('my-cool-div')
myCoolDiv.textContent = 'I have a blue line'
myCoolDiv.style = 'width: 100%; height: 200px;'
document.body.appendChild(myCoolDiv)

const span = document.createElement('span')
span.textContent = `I'm not a blue line`
span.style = 'width: 100%; height: 200px;'
document.body.appendChild(span) 
```

Enter fullscreen mode Exit fullscreen mode

开始看到魔法了吗？您可以添加多个包含不同文本的`<my-cool-div>`，甚至其他节点。所有东西都将被一条蓝线居中！在这一页里玩一会儿，试着在阴影根里添加一些其他的东西。

在本文的下一部分，我们将看到原生 web 组件背后的两个核心概念。我们还将使用一些工具来使我们的生活变得更容易(因为手动完成所有这些是相当乏味的)。