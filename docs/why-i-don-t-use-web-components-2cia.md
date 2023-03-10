# 为什么我不使用 web 组件

> 原文：<https://dev.to/richharris/why-i-don-t-use-web-components-2cia>

对于我在 dev.to 上的第一篇文章，我想我应该写一个不错的、安全的、没有争议的话题:web 组件。

我主要是为未来的自己写这篇文章，这样下次有人问我为什么是 web 组件怀疑论者，为什么默认情况下[svelete](https://svelte.dev)不能编译成定制元素时，我就有话可说了。(它*能*编译到 CEs，它能消费 CEs 从它在[自定义元素无处不在](https://custom-elements-everywhere.com/)上的满分就可见一斑。)

所有这些都不应该被看作是对 web 组件所做的艰苦工作的批评。我可能在这篇文章中犯了一些错误，如果是这样的话，我欢迎大家指正。

我也不是说你不应该使用 web 组件。他们*确实*有有效的用例。我只是在解释为什么*我*没有。

## 1。渐进增强

这可能是一个越来越过时的观点，但我认为网站应该尽可能不使用 JavaScript。Web 组件不会。

这对于本质上是交互的东西来说很好，比如自定义表单元素(`<cool-datepicker>`)，但是对于你的导航条来说就不好了。或者考虑一个简单的`<twitter-share>`元素，它封装了构建 [Twitter web intent](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/guides/web-intent.html) URL 的所有逻辑。我可以[用苗条的](https://svelte.dev/repl/98aa20d4cb3d40dabfef7d8dae183b85?version=3.5.2)构建它，它会生成服务器渲染的 HTML，如下所示:

```
<a target="_blank" noreferrer href="..." class="svelte-1jnfxx">
  Tweet this
</a> 
```

换句话说，一个普通的`<a>`元素，在它所有可及的荣耀中。

启用 JavaScript 后，它逐渐增强——不是打开一个新标签，而是打开一个小弹出窗口。但是没有，它仍然工作正常。

相比之下，web 组件 HTML 应该是这样的...

```
<twitter-share text="..." url="..." via="..."/> 
```

...如果 JS 被禁用或由于某种原因被破坏，或者用户使用的是旧的浏览器，那么它是无用的和不可访问的。

`class="svelte-1jnfxx"`是没有影子 DOM 的封装样式。这就引出了我的下一个观点:

## 2。CSS 输入，错误...射流研究…

如果您想使用 Shadow DOM 进行样式封装，您必须将 CSS 包含在一个`<style>`元素中。唯一可行的方法是，至少如果您想避免 FOUC，将 CSS 放在定义定制元素的 JavaScript 模块的字符串中。

这与我们得到的性能建议背道而驰，这些建议可以概括为“请少用 JavaScript”。特别是 CSS-in-JS 社区因为没有将 CSS 放在`.css`文件中而受到批评，然而我们在这里。

将来，我们也许可以使用 [CSS 模块](https://github.com/w3c/webcomponents/issues/759)和[可构造样式表](https://developers.google.com/web/updates/2019/02/constructable-stylesheets)来解决这个问题。我们也许可以使用`::theme`和`::part`来设计 Shadow DOM 内部的样式。但是这些也不是没有问题。

## 3。平台疲劳

> ![Rich Harris profile image](img/bb52b451f45377440ef4090bbb10ccda.png)

在撰写本文时，Chromium bug 追踪器[https://crbug.com](https://crbug.com)上有 61000 个未决问题，这反映了构建一个现代网络浏览器的巨大复杂性。

每当我们向平台添加一个新功能，我们就增加了复杂性——为 bug 创造了新的空间，使得 Chromium 的新竞争对手出现的可能性越来越小。

这也给开发人员带来了复杂性，他们被鼓励学习这些新特性(其中一些，像 HTML 导入或原始的自定义元素规范，在 Google 之外从未流行起来，最终又被删除了。)

## 4。多填充物

如果你想支持所有的浏览器，你需要使用 polyfills，这并没有什么帮助。由谷歌人写的关于[可构造样式表](https://developers.google.com/web/updates/2019/02/constructable-stylesheets)的文献真的没有帮助(嗨，杰森！)，没有提到它们是 Chrome 独有的特性(*编辑:在我打开一个[拉取请求](https://github.com/google/WebFundamentals/pull/8212)* 后，这个问题已经被修复)。三个规范编辑[都是谷歌人。Webkit](https://wicg.github.io/construct-stylesheets/) [似乎对设计的某些方面有一些疑问](https://github.com/mozilla/standards-positions/issues/103#issuecomment-494181931)。

## 5。作文

对于一个组件来说，能够控制何时(或是否)呈现其分时段内容是很有用的。假设我们想要使用 [`<html-include>`元素](https://github.com/justinfagnani/html-include-element)来显示一些来自网络的文档，当它变得可见时:

```
<p>Toggle the section for more info:</p>
<toggled-section>
  <html-include src="./more-info.html"/>
</toggled-section> 
```

惊喜！尽管您还没有打开这个部分，浏览器已经请求了`more-info.html`，以及它链接到的任何图片和其他资源。

这是因为分时段内容急切地在自定义元素中呈现*。原来大部分时候你希望分时段的内容懒洋洋地渲染*。Svelte v2 采用了 eager 模型以符合 web 标准，这被证明是一个主要的挫折来源——例如，我们无法创建一个等效的 React 路由器。在 Svelte v3 中，我们放弃了自定义元素组合模型，并且从未回头。**

 **不幸的是，这只是 DOM 的一个基本特征。这就把我们带到了...

## 6。道具和属性的混淆

道具和属性基本是一回事吧？

```
const button = document.createElement('button');

button.hasAttribute('disabled'); // false
button.disabled = true;
button.hasAttribute('disabled'); // true

button.removeAttribute('disabled');
button.disabled; // false 
```

我是说，差不多:

```
typeof button.disabled; // 'boolean'
typeof button.getAttribute('disabled'); // 'object'

button.disabled = true;
typeof button.getAttribute('disabled'); // 'string' 
```

然后还有不匹配的名字...

```
div = document.createElement('div');

div.setAttribute('class', 'one');
div.className; // 'one'

div.className = 'two';
div.getAttribute('class'); // 'two' 
```

...而那些看起来根本不对应的:

```
input = document.createElement('input');

input.getAttribute('value'); // null
input.value = 'one';
input.getAttribute('value'); // null

input.setAttribute('value', 'two');
input.value; // 'one' 
```

但是我们可以忍受这些怪癖，因为*当然*在字符串格式(HTML)和 DOM 之间的转换中会丢失一些东西。它们的数量是有限的，并且都有文档记录，所以只要有足够的时间和耐心，至少您可以了解它们。

Web 组件改变了这一点。不仅不再对属性和道具之间的关系有任何保证，而且作为一个 web 组件作者，你(大概？)理应支持两者。这意味着你会看到这样的事情:

```
class MyThing extends HTMLElement {
  static get observedAttributes() {
    return ['foo', 'bar', 'baz'];
  }

  get foo() {
    return this.getAttribute('foo');
  }

  set foo(value) {
    this.setAttribute('foo', value);
  }

  get bar() {
    return this.getAttribute('bar');
  }

  set bar(value) {
    this.setAttribute('bar', value);
  }

  get baz() {
    return this.hasAttribute('baz');
  }

  set baz(value) {
    if (value) {
      this.setAttribute('baz', '');
    } else {
      this.removeAttribute('baz');
    }
  }

  attributeChangedCallback(name, oldValue, newValue) {
    if (name === 'foo') {
      // ...
    }

    if (name === 'bar') {
      // ...
    }

    if (name === 'baz') {
      // ...
    }
  }
} 
```

有时你会看到事情向相反的方向发展— `attributeChangedCallback`调用属性访问器。不管怎样，人类工程学是灾难性的。

相比之下，框架有一种简单而明确的方式将数据传递给组件。

## 7。泄漏设计

这一点有点模糊，但是让我奇怪的是`attributeChangedCallback`只是元素实例上的一个方法。你可以这样做:

```
const element = document.querySelector('my-thing');
element.attributeChangedCallback('w', 't', 'f'); 
```

没有属性发生变化，但它的行为就像发生了变化一样。当然，JavaScript 总是为恶作剧提供大量的机会，但是当我看到实现细节像这样被戳穿时，我总是觉得他们试图告诉我们设计不太正确。

## 8。DOM 是坏的

好了，我们已经确定 DOM 是坏的。但是对于构建交互式应用来说，这是一个多么尴尬的界面啊。

几个月前，我写了一篇名为 [Write less code](https://svelte.dev/blog/write-less-code) 的文章，旨在说明 Svelte 如何让你比 React 和 Vue 这样的框架更有效地构建组件。但我没有和 DOM 对比。我应该知道的。

概括一下，这里有一个简单的`<Adder a={1} b={2}/>`组件:

```
<script>
  export let a;
  export let b;
</script>

<input type="number" bind:value={a}>
<input type="number" bind:value={b}>

<p>{a} + {b} = {a + b}</p> 
```

这就是全部。现在，让我们构建一个 web 组件:

```
class Adder extends HTMLElement {
  constructor() {
    super();

    this.attachShadow({ mode: 'open' });

    this.shadowRoot.innerHTML = `
      <input type="number">
      <input type="number">
      <p></p>
    `;

    this.inputs = this.shadowRoot.querySelectorAll('input');
    this.p = this.shadowRoot.querySelector('p');

    this.update();

    this.inputs[0].addEventListener('input', e => {
      this.a = +e.target.value;
    });

    this.inputs[1].addEventListener('input', e => {
      this.b = +e.target.value;
    });
  }

  static get observedAttributes() {
    return ['a', 'b'];
  }

  get a() {
    return +this.getAttribute('a');
  }

  set a(value) {
    this.setAttribute('a', value);
  }

  get b() {
    return +this.getAttribute('b');
  }

  set b(value) {
    this.setAttribute('b', value);
  }

  attributeChangedCallback() {
    this.update();
  }

  update() {
    this.inputs[0].value = this.a;
    this.inputs[1].value = this.b;

    this.p.textContent = `${this.a} + ${this.b} = ${this.a + this.b}`;
  }
}

customElements.define('my-adder', Adder); 
```

是啊。

还要注意，如果你同时改变`a`和`b`，将会导致两次独立的更新。框架一般不会遇到这个问题。

## 9。全局名称空间

我们不需要在这个问题上纠缠太多；一段时间以来，人们已经很好地理解了拥有单一共享名称空间的危险，这就足够了。

## 10。这些都是已经解决的问题

最大的挫折是我们已经有了非常好的组件模型。我们仍在学习，但基本问题——通过以面向组件的方式操作 DOM 来保持视图与某些状态同步——已经解决了很多年。

然而，我们正在向平台添加新的功能，只是为了让 web 组件与我们在用户领域已经可以做的事情对等。

给定有限的资源，花在一项任务上的时间意味着没有花在另一项任务上的时间。尽管开发人员基本上漠不关心，但还是在 web 组件上花费了大量的精力。如果这些精力用在了其他地方，网络会取得什么样的成就？**