# 将 React 组件包装在自定义元素中

> 原文：<https://dev.to/gilfink/wrapping-react-components-inside-custom-elements-3fph>

[![](img/6f4124b4963342161748b499ce6e563c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DOcx1pPY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/192/0%2Ay76eKTDqc8wkQceA.png) 

<figcaption>网页组件</figcaption>

本周，我很高兴在 2019 年 react next 大会上发言。我的演讲名为“我与 Web 组件在一起，Web 组件也与我在一起”，主要是关于在 React 应用中使用 Web 组件，以及用自定义元素包装 React 组件。在这篇文章中，我将解释第二部分，以及为什么你可能想这样做。当这个视频可以在网上看到的时候，我会把它放在这个帖子里。

#### 与 Web 组件发生反应

在 React 文档中有一节是关于 [React 和 Web 组件](https://reactjs.org/docs/web-components.html)的。在那一节中，提到了 React 和 Web 组件是互补的。React 是一个视图引擎，负责保持 DOM 与应用程序的数据同步，而 Web 组件为创建可重用的 HTML 组件提供了强大的封装。但是在现实世界中，我所咨询的大多数公司并没有同时使用这两种方法，这是为什么呢？

*   开发人员仍然对 Web 组件 API 心存疑虑，更喜欢使用成熟的框架/库。
*   Web 组件 API 在一些浏览器中仍然没有实现，这意味着为了使用它们，我们需要加载一个 polyfill 代码。
*   作为开发人员，我们已经习惯了框架/库的好处，比如数据绑定、反应性、延迟加载等等。在 Web 组件中，我们需要精心制作一切，样板文件有时很麻烦。

那么，为什么要投资 Web 组件呢？我已经在 2017 年写了一篇关于这一点的帖子，名为“[为什么我押注于 Web 组件(你也应该考虑使用它们)](https://medium.com/@gilfink/why-im-betting-on-web-components-and-you-should-think-about-using-them-too-8629396e27a)”，你可以在那里读到我的想法。总结一下我所写的——Web 组件可以帮助你将组件的实现与框架/库分离，并帮助你在组件和它们的消费应用程序之间创建一个边界。它们也适用于设计任何框架/库都可以使用的系统构建。

#### 将 React 组件包装在自定义元素内

现在我们明白了为什么我们要使用 Web 组件，让我们来谈谈如何使用 Web 组件 API 来包装 React 组件。

> 注意:所有代码都是用 TypeScript 编写的，但是不要担心，因为很容易将其翻译成普通的 JavaScript。

我们将从一个用 React 编写的简单可折叠面板开始:

```
import * as React from 'react';

interface IProps {
  title: string;
}

interface IState {
  isCollapsed: boolean;
}

export default class CollapsibleReact extends React.Component<IProps, IState> {
  state: Readonly<IState> = {
    isCollapsed: false
  };

  public toggle = () => {
    this.setState(prevState => ({
      isCollapsed: !prevState.isCollapsed
    }));
  }

  public render() {
    const { isCollapsed } = this.state;
    const { title, children } = this.props;
    return (
      <div style={{ border: 'black dashed 1px' }}>
        <header onClick={ this.toggle } style={{ backgroundColor: 'blue', color: 'white' }}>{title}</header>
        <section hidden={isCollapsed}>
          {children}
        </section>
      </div>
    );
  }
} 
```

该组件包括一个可折叠部分和一个 header 元素，单击该元素可以在折叠和显示状态之间切换。如果我们想将这个组件包装在一个自定义元素中，我们必须注意一些事情:

*   通过标题和儿童道具。
*   当标题道具改变时重新渲染。

我们将首先创建自定义元素类，并在[**CustomElementRegistry**](https://medium.com/@gilfink/understanding-the-customelementregistry-object-74408a25a3d4)中定义它:

```
export default class CollapsiblePanel extends HTMLElement{

}

window.customElements.define('collapsible-panel', CollapsiblePanel); 
```

我们的类将包括两个成员:标题和挂载点，它们将负责在 DOM 中保存挂载点:

```
mountPoint: HTMLSpanElement;
title: string; 
```

现在让我们来谈谈主要的实现点——安装 React 组件。我们将使用定制元素的 **connectedCallback** 生命周期事件来完成这项工作:

```
connectedCallback() {
  this.mountPoint = document.createElement('span');
  const shadowRoot = this.attachShadow({ mode: 'open' });
  shadowRoot.appendChild(this.mountPoint);

  const title = this.getAttribute('title');
  ReactDOM.render(this.createCollapsed(title), this.mountPoint);
  retargetEvents(shadowRoot);
} 
```

在 **connectedCallback** 中，我们将创建一个跨度，作为我们的挂载点。然后，我们将使用 **attachShadow** 函数来创建一个阴影根，它将成为应用程序和 React 组件之间的边界。我们将把挂载点附加到影子根。在我们设置好所有的背景之后，我们将使用 **ReactDOM** 来渲染 React 组件(使用 **createCollapsed** 函数，你马上就会看到)。最后但同样重要的是，我们将使用一个名为 **retargetEvents** 的函数，它是**react-shadow-DOM-retarget-events**模块的一部分。我们将在这篇文章的后面讨论我为什么使用**重定向事件**，所以请继续阅读:)

让我们看看 **createCollapsed** 函数:

```
createCollapsed(title) {
  return React.createElement(CollapsibleReact, { title }, React.createElement('slot'));
} 
```

该函数获取 React 组件将使用的标题。然后，该函数使用 React 的 **createElement** 函数来创建 **CollapsibleReact** 组件实例。 **createElement** 也接收道具对象作为第二个参数，子道具作为第三个参数。为了按预期传递子组件，我使用 HTML slot 元素在包装组件子组件和被包装组件子组件之间架起了一座桥梁。

现在我们已经完成了包装组件的安装，下一步是在标题改变的情况下重新呈现组件。为此，我们将使用观察到的属性和 **attributeChangedCallback** 定制元素生命周期事件。下面是它们在组件中的使用方式:

```
static get observedAttributes() {
  return ['title'];
}

attributeChangedCallback(name, oldValue, newValue) {
  if(name === 'title') {
    ReactDOM.render(this.createCollapsed(newValue), this.mountPoint);
  }
} 
```

当标题改变时，我们再次使用 **ReactDOM** **render** 函数。因为我们保存了挂载点， **ReactDOM** 将会完成所有的重渲染工作，并为我们计算差异。

自定义元素的整个实现:

```
import * as React from 'react';
import * as ReactDOM from 'react-dom';
import * as retargetEvents from 'react-shadow-dom-retarget-events';
import CollapsibleReact from './collapsible-react';

export default class CollapsiblePanel extends HTMLElement {
  static get observedAttributes() {
    return ['title'];
  }

  mountPoint: HTMLSpanElement;
  title: string;

  createCollapsed(title) {
    return React.createElement(CollapsibleReact, { title }, React.createElement('slot'));
  }

  connectedCallback() {
    this.mountPoint = document.createElement('span');
    const shadowRoot = this.attachShadow({ mode: 'open' });
    shadowRoot.appendChild(this.mountPoint);

    const title = this.getAttribute('title');
    ReactDOM.render(this.createCollapsed(title), this.mountPoint);
    retargetEvents(shadowRoot);
  }

  attributeChangedCallback(name, oldValue, newValue) {
    if (name === 'title') {
      ReactDOM.render(this.createCollapsed(newValue), this.mountPoint);
    }
  }
}

window.customElements.define('collapsible-panel', CollapsiblePanel); 
```

#### 重新定位 React 事件

为什么我要使用**重定向事件**函数？React 事件系统依赖于[合成事件](https://reactjs.org/docs/events.html)，它们是浏览器本地事件之上的包装器。React 中的所有事件都汇集在一起，并将注册到文档本身。当您使用影子 DOM 时，这种行为可能会很成问题。在影子 DOM 中，影子 DOM 片段存在于它自己的 DOM 片段中。这意味着 React 事件不会在阴影部分内工作。 **retargetEvents** 函数有助于在影子 DOM 中注册事件，并使它们按预期工作。

#### 测试包装

现在我们可以测试包装组件了。我使用了一个 Angular 应用程序来使用组件，这是我在应用程序主 HTML 中使用的代码:

```
<div style="text-align: center">
  <h1>
    Welcome to {{ title }}!
  </h1>
  <img width="300" alt="Angular Logo" src="...">
</div>
<collapsible-panel [title]="title">  
  <ul>  
    <li>Web Components rules</li>  
    <li>Look I'm wrapping a React component</li>  
  </ul>  
</collapsible-panel> 
<router-outlet></router-outlet> 
```

运行应用程序的结果:

[![](img/64c7eac030762ed673b46f0ed721aa94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q3pYXWFL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaZK47U5nBRKKvBARnw6rUg.png) 

<figcaption>Angular app 托管一个 React 包装组件</figcaption>

#### 总结

在这篇文章中，我使用 Web 组件 API 来包装一个 React 组件，并从 Angular 应用程序中使用它。当然，这是做到这一点的一种方式，我可以考虑如何使它更加自动化，但这是另一篇文章的主题:)

让我知道你在评论中的想法。