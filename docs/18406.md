# 现代 Web 组件

> 原文：<https://dev.to/samthor/modern-web-components-37hf>

我是[圣诞老人追踪者](https://santatracker.google.com)的主角。是的，我知道现在是六月——离假期非常远。💼

我想谈谈 Web 组件。快速复习一下:这些自定义元素可能会使用 Shadow DOM，允许您自己命名的元素包含自己的 CSS、样式和 DOM:

```
<div>
  <my-custom-element></my-custom-element>
  <p>Mixed with regular HTML!</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 聚合物掉了👋

我们更新 Santa 的核心 UI 以*移除*[聚合物](https://github.com/Polymer/polymer)网络组件库的原因之一是因为聚合物是*粘性的*。聚合物只有在与它相互作用的所有其他元素都是聚合物时才真正起作用:它接触的任何东西也需要以同样的方式起作用。

这是不可扩展的，也没有给我们将来移动的空间。像[WebComponents.org](https://www.webcomponents.org/)这样的网站，在谷歌对聚合物的宣传达到高潮时发布，宣称#UseThePlatform，但我怀疑那里的大多数元素都以同样的方式具有粘性。

## 平滑元素😎

我们用`lit-element`重写圣诞老人追踪器核心用户界面的一个主要原因是因为不像聚合物，Lit 是*而不是*粘性的。它只是一个助手库，可以与页面上的任何其他元素互换使用。🤝

因此，在重写圣诞老人跟踪器时，我们发现许多元素不需要继承内置`HTMLElement`类以外的任何东西，因为它们只是简单的构建块。我们称这些为“香草”元素。🍨

除此之外，还有各种各样的小型或大型 Web 组件库充当助手。我的 IRL 好友 Trey 写了 [SkateJS](https://skatejs.netlify.com/) ，只要在 dev.to 上搜索 [#webcomponents](https://dev.to/t/webcomponents) 标签，就会发现一堆候选人。🔎

当然，您可能不应该发布几个不同的库:这样做很明智，既可以节省字节，又不会使代码过于复杂。但是如果你某一天使用 Lit，但另一天使用 Skate 重写(也有一点香草味)，你可以安全地让这些库在迁移过程中共存，这样你的站点永远不会不可用。🤗

### 举个例子🔥

为了完整起见，让我们展示一个元素在 Lit 中的样子:

```
class SimpleGreeting extends LitElement {
  static get properties() {
    return { name: { type: String } };
  }

  constructor() {
    super();
    this.name = 'World';
  }

  render() {
    return html`<p>Hello, ${this.name}!</p>`;
  }
}
customElements.define('simple-greeting', SimpleGreeting); 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？SkateJS 有一个类似的，简单的，[入门示例](https://skatejs.netlify.com/docs/element)。🛹

### 香草示例🍦

没有任何库，仅仅使用平台
，一个简单的元素看起来会是什么样子

```
class SantaChoiceElement extends HTMLElement {
  constructor() {
    super();

    const template = Object.assign(document.createElement('template'), {
      innerHTML: `
<style>/* CSS here */</style>
<div class="wrap">
  <!-- more HTML -->
</div>
`,
    });

    // polyfill for CSS in Shadow DOM
    if (self.ShadyCSS) {
      self.ShadyCSS.prepareTemplate(template, 'santa-choice');
    }
    this.attachShadow({mode: 'open'});
    this.shadowRoot.appendChild(document.importNode(template.content, true));
  }
}
customElements.define('santa-choice', SantaChoiceElement); 
```

Enter fullscreen mode Exit fullscreen mode

对于不支持 Shadow DOM 的 [~10%的用户](https://caniuse.com/#feat=shadowdomv1)，这段代码看起来很复杂(对于 Shady CSS 有多种填充)。Lit 正在“免费”包括这个。🆓

<small>作为旁白；`<santa-choice>`是一个我非常自豪的元素，它驱动着[精灵制造者🧝](https://santatracker.google.com/elfmaker.html) 底部的选择器。我希望有一天能写出它是如何工作的。</small>

## 谢谢！

我希望这能让你对 WCs (Web 组件)有所了解。对我来说，绝对深刻的时刻是当我意识到使用 Lit 或其他库的好处是它不是全包的:它们与生态系统的任何其他部分都很好地配合，你可以根据自己的喜好使用它。👍

Sixteen