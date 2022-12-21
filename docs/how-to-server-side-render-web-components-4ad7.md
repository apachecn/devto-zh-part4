# 如何在服务器端呈现 Web 组件

> 原文：<https://dev.to/lamplightdev/how-to-server-side-render-web-components-4ad7>

几乎所有的现代框架都提供了一种方式，通过在 JavaScipt 服务器端框架(如 express)上运行框架代码，产生一个可以发送给浏览器的 HTML 的**初始字符串，来**服务器端呈现** (SSR)一个网站。在浏览器中运行的相同组件代码也在服务器上运行。**

对于 Web 组件，SSR 有几个限制:

*   不可能在服务器上运行相同的代码，因为 **Web 组件依赖于浏览器特定的 DOM API**,除非你启动一个无头浏览器或替代的 DOM 实现，否则它们是不可用的。这两种解决方案都带来了不小的开销，很难证明其合理性。
*   影子 DOM 不能([目前是](https://github.com/whatwg/dom/issues/510))以声明方式表示，所以你不能在 HTML 的初始字符串中发送它。相反，你需要实现一些其他风格的封装解决方案(就像框架一样。)

因此，与框架组件相比，Web 组件无疑处于劣势，但它们仍然提供了两个主要优势:

*   更少的代码运行更快，因为他们使用内置的平台 API
*   经得起未来考验，因为它们可以在任何地方使用，包括在当前和未来的框架中，无需修改或没有过时的风险。

所以如果这些优势足够让你选择 Web 组件而不是框架组件，**你怎么能 SSR Web 组件呢？**因为没有影子 DOM 的 **Web 组件只是标准的 HTML** ，所以很简单。

我们将使用一个简单的 Twitter 分享按钮作为例子。要查看最终结果，请看一下[演示](https://grave-mirror.glitch.me/)(或[浏览代码](https://glitch.com/edit/#!/grave-mirror?path=public/twitter-share.html:1:0))并尝试启用和不启用 JavaScript。

### [客户端上的](#on-the-client)

**注意**:这是一个简化的实现，它使用初始属性来呈现组件，与更新的属性或特性没有反应——但是没有理由不能在稍后阶段使用普通 JavaScript 或第三方库(例如 [lit-html](https://lit-html.polymer-project.org/) 和/或 [lit-element](https://lit-element.polymer-project.org/) )将它添加到特定于浏览器的代码部分。如果你真的走这条路，也检查一下 lit-html-server ，让你在服务器上使用 lit-html 语法。

我们的**初始的、基于浏览器的 web 组件**可能看起来像这样:

#### twitter-share.js

```
class TwitterShare extends HTMLElement {
  connectedCallback() {
    // set up the props based on the inital attribute values
    this.props = [...this.attributes].reduce((all, attr) => {
      return {
        ...all,
        [attr.nodeName]: attr.nodeValue
      };
    }, {});

    this.render();
  }

  render() {
    // set the innerHTML manually - ShadowDOM can't be used
    this.innerHTML = this.template();

    // add an event listener to the link inside the component
    const a = this.querySelector('a');
    a.addEventListener('click', this.open);
  }

  template() {
    // create the HTML needed for the component
    const { text, url, hashtags, via, related } = this.props;

    const query = [
      text && `text=${encodeURIComponent(text)}`,
      url && `url=${encodeURIComponent(url)}`,
      hashtags && `hashtags=${hashtags}`,
      via && `via=${encodeURIComponent(via)}`,
      related && `related=${encodeURIComponent(related)}`
    ]
      .filter(Boolean)
      .join('&');

    const href = `https://twitter.com/intent/tweet?${query}`;

    return `
      <a target="_blank" noreferrer href="${href}">
        Tweet this
      </a>
    `;
  }

  open(event) {
    // open the twitter share url in a popup window when the link is clicked
    event.preventDefault();

    const a = event.target;
    const w = 600;
    const h = 400;
    const x = (screen.width - w) / 2;
    const y = (screen.height - h) / 2;
    const features = `width=${w},height=${h},left=${x},top=${y}`;

    window.open(a.getAttribute('href'), '_blank', features);
  }
}

customElements.define('twitter-share', TwitterShare); 
```

然而，要在服务器上渲染它，你不能使用`HTMLElement`、`customElements`或`addEventListener`，因为这些**API 在服务器**上不存在，所以你需要**将模板方法提取到一个可以使用的独立函数**:

#### [T1】Twitter-share-template . mjs](#twittersharetemplatemjs)

```
// .mjs is used here so javascript modules can be used on the server
export default props => {
  // the same logic as above, in a self contained function
  const { text, url, hashtags, via, related } = props;

  const query = [
    text && `text=${encodeURIComponent(text)}`,
    url && `url=${encodeURIComponent(url)}`,
    hashtags && `hashtags=${hashtags}`,
    via && `via=${encodeURIComponent(via)}`,
    related && `related=${encodeURIComponent(related)}`
  ]
    .filter(Boolean)
    .join('&');

  const href = `https://twitter.com/intent/tweet?${query}`;

  return `
    <a target="_blank" noreferrer href="${href}">
      Tweet this
    </a>
  `;
}; 
```

并且**更新我们基于浏览器的组件定义**以导入和使用该函数:

#### twitter-share.js

```
import template from './twitter-share-template.mjs';

class TwitterShare extends HTMLElement {
  //...as before

  render() {
    this.innerHTML = template(this.props);

    // ...as before
  }

  // template() -> removed

  // ...as before
}

customElements.define('twitter-share', TwitterShare); 
```

### [服务器上的](#on-the-server)

在服务器上，当构建页面响应时，您需要为我们的组件使用带有必要属性的 **HTML 标签，这些属性将被浏览器解析，并且为组件的 **SSR 内容使用与上面相同的`template`函数**:**

#### server.mjs

```
import template from './public/js/twitter-share-template.mjs';

// ... the server side route

const props = {
  text: 'A Twitter share button with progressive enhancement',
  url: 'https://grave-mirror.glitch.me',
  via: 'lamplightdev'
};

// be sure to sanatize the props if including directly in HTML

response.write(`
  <twitter-share text="${props.text}" url="${props.url}" via="${props.via}"> ${template(props)} </twitter-share>
`);

// ... 
```

要查看组件的运行情况，请看一下[演示](https://grave-mirror.glitch.me/)和[浏览代码](https://glitch.com/edit/#!/grave-mirror?path=public/twitter-share.html:1:0)。请务必在启用和不启用 JavaScript 的情况下尝试该页面。

总之，**您可以在没有任何服务器端 DOM 实现的情况下对 web 组件进行 SSR，但只能在没有影子 DOM 的情况下进行。**

如果你有任何反馈或问题，我很乐意倾听。

[订阅邮件列表以获得关于 Web 组件和构建高性能网站的新帖子的通知](http://eepurl.com/gvjFwv)