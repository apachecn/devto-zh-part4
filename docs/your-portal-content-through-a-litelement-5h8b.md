# 您的门户内容通过 LitElement

> 原文：<https://dev.to/westbrook/your-portal-content-through-a-litelement-5h8b>

[![](img/3122106aa631afd96b57abf852748580.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B19r6J67--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c7x8ty8v9waf8serhpii.png)

当我们最后一次遇见时，我们通过[门户](https://reactjs.org/docs/portals.html)发送内容，就好像我们是[杰克·奥尼尔](https://en.wikipedia.org/wiki/Jack_O%27Neill)向其他星系派遣士兵。不仅如此，由于有了 [Shadow DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM) 和 [Custom Events](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) 的帮助，我们用普通的 javascript 来做这件事，而不是像我们检查过的一些现有技术的坚实例子那样用框架。如果你没有这个荣幸，或者忘记了你读过的大部分内容(加入俱乐部)，不要担心，我们其他人会在这里等你...

...[您在影子 DOM 门户中的内容](https://dev.to/westbrook/your-content-in-shadow-dom-portals-3cdb)...

...好了，现在我们都准备好了，没有理由埋葬 lede，今天我们将讨论当利用简单基类的支持来创建快速、轻量级的 web 组件时，那些相同的技术看起来像什么；[元素](https://lit-element.polymer-project.org/)。所以，没有进一步的麻烦，这里是它所有的荣耀:

[https://stackblitz.com/edit/lit-element-portal?embed=1&view=preview&](https://stackblitz.com/edit/lit-element-portal?embed=1&view=preview&)

好吧，也许不是它的全部荣耀，更像是它对现实的一对一移植，在上一篇文章中讨论并交付了完全普通的 JS。在前一篇文章的 [*声明式 API*](https://dev.to/westbrook/your-content-in-shadow-dom-portals-3cdb#declarative-api) 部分，我们已经看到了其中的一些，但是现在重新回顾它是很重要的，因为它将形成扩展元素的基础，以支持在整个 [*中讨论的思想，但是，现在呢？*](https://dev.to/westbrook/your-content-in-shadow-dom-portals-3cdb#but-now-what) 节其中。所以，让我们一起努力，走向辉煌！

# 跨浏览器支持

这是任何可能的产品代码中的一大块，出于概念验证的原因，我选择在我们之前的对话中略去。[我们讨论了一些可能性](https://dev.to/westbrook/your-content-in-shadow-dom-portals-3cdb#cross-browser-support)，但直到现在都没有深入探讨。我们会遇到的第一个问题是关于`slot.assignedNodes()`的使用。你可能还记得我们之前一直在使用`slot.assignedElements()`，然而，我们希望能够得到松散的文本节点以及元素，所以`assignedNodes`是一条路要走。让我们看看依赖于此的代码现在是什么样子的:

```
 projectSlot(e) {
        if (!e.target.assignedNodes().length) return;
        this.dispatchEvent(createEvent('portal-open', {
            destination: this.destination,
            content: e.target.assignedNodes(),
        }));
    } 
```

您可能还记得，当在多填充设置中依赖 ShadyDOM 时，没有对`assignedNodes`的支持，因此我们需要做一些额外的工作，以便跨浏览器启用相同的功能。实际上两行代码对我们的目标征收了这么多税，这是多么可悲啊，但是不用担心，我们可以通过 via `[...el.childNodes]`在这种情况下获得类似的结果。虽然在*大多数*情况下这可以做到这一点，但是因为使用了没有`name`属性的`<slot />`标签，我们需要在将内容传递给我们的`<portal-destination />`之前过滤掉一些可能的误报。

```
 get portalContent() {
        const slot = this.shadowRoot.querySelector('slot');
        return slot && slot.assignedNodes
            ? slot.assignedNodes()
            : this.childrenWithoutSlots;
    }
    get childrenWithoutSlots() {
        let nodes = [...(this.childNodes.length ? this.childNodes : [])];
        nodes = nodes.filter(
            node => node.slot === '' || node.slot === null
        );
        return nodes;
    }
    projectSlot() {
        let content = this.portalContent;
        if (!content.length) return;
        this.dispatchEvent(createEvent('portal-open', {
            destination: this.destination,
            content: content,
        }));
    } 
```

如果您有兴趣在现实生活中遵循上面的代码，有几种方法可以访问旧的浏览器。核选项与类似 [BrowserStack](https://www.browserstack.com/) 的工具一起工作，或者你可以依赖微软为[各种版本的 Internet Explorer 和 Edge](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/) 提供的虚拟机之一，但我目前的首选是 [Firefox:扩展支持版本](https://www.mozilla.org/en-US/firefox/organizations/)。Firefox ESR 是 Firefox 的一个面向企业的版本，目前发布的是版本 60，最初是在 Firefox 支持 v1 web 组件规范之前发布的。这并没有让调试变得非常有趣，因为我还没有弄清楚如何打开开发工具，但是`alert()`工作得很好，我已经利用了它，比我想承认的还要多...

在跨浏览器支持的领域中，我们要讨论的剩余上下文是当内容到达门户的目的地时对其应用样式。这就是事情变得棘手的地方，迫使我们权衡各种前进道路的利弊。默认情况下,`LitElement`将确保 ShadyCSS 应用于多填充上下文中的组件。ShadyCSS 在尚不支持该规范的浏览器中模拟基于阴影 DOM 的样式封装，这是一个随着 IE11 和 Edgium Edge 之前的 sun 设置而不断缩短的浏览器列表。它是在正确性和性能的交叉点上做到这一点的，方法是将针对相关组件的样式的单一作用域版本写入全局作用域。这对于维护基于影子 DOM 的样式的“样式作用于元素”契约大有帮助；然而，它有两个主要的缺点。第一个问题是没有明确解决“防止外部选择器”的契约，这意味着所有来自影子 DOM 之外的样式都有能力泄漏到组件中。第二个问题在我们的`portal-destination`定义的上下文中尤其麻烦，默认情况下，应用于自定义元素的 shadow DOM 的所有实例的样式必须相同。

在应用程序生命周期中的每一段投影内容*都可以*进行定制样式，这在我们目前工作的上下文中是很棘手的，我们将内容直接应用于`<portal-entrace/>`元素:

```
 <portal-entrance destination="style-demo">
        <style>button{background: red;}</style>
        <h1>Send This Content</h1>
        <p>Hello world! From my-element ${this.counter}</p>
        <button @click=${this.increase}>+1</button>
    </portal-entrance> 
```

对于在此上下文中定义的应用于`portal-destination`元素的`<style/>`,我们需要在`LitElement`实现的顶部进行工作，以通过 ShadyCSS polyfill 正确地限定该内容的范围。此外，`<style/>`元素在运行时不需要在父元素的`shadowRoot`中，以确保它不会被父元素消耗，就好像那些样式是为它准备的一样。解决这个问题最直接的方法是将我们希望通过门户发送的内容包装在一个自定义元素中:

```
 <portal-entrance destination="destination">
        <content-to-be-ported-element></content-to-be-ported-element>
    </portal-entrance> 
```

然而，这对潜在用途的限制是非常禁止的:

1.  不要把`<style/>`元素直接放到你`<portal-entrance/>`的 light DOM 中。
2.  当试图通过门户发送样式数据时，将其包装在一个元素中，即 la `<content-to-be-ported-element/>`。
3.  在父元素和`<content-to-be-ported-element/>`之间手动连接状态管理。
4.  等等。

虽然每一段定义良好的代码都需要一个你不能用它做的事情的列表，但我觉得这是一座太远的桥。我们应该能够将这些向后拨一点，并允许我们以更大的灵活性来提供这一功能。我们在这里要解决的主要问题是能够将`<style/>`元素直接放入`<portal-entrance/>`元素，并将这些样式应用于它们被发送到的`<portal-destination/>`元素。幸运的是，无论你是使用`@webcomponents/webcomponentsjs/webcomponents-bundle.js`还是它的精简版兄弟`@webcomponents/webcomponentsjs/webcomponents-loader.js`来确保跨浏览器支持，它们都将确保没有原生阴影 DOM 支持的浏览器被交付 ShadyCSS polyfill。

ShadyCSS polyfill 提供了一个 API，通过该 API 可以准备模板和样式，以近似文档其余部分中类似的聚合填充阴影根中内容的封装。我们可以使用它在`LitElement`提供的基础上做额外的工作，以确保通过我们的门户发送的`<style/>`内容得到相同的处理。该过程包括以下步骤:

1.  确保 polyfill 不存在或目前未被使用。
2.  创建一个模板来准备样式。
3.  收集将成为`<portal-destination/>`元素的直接子元素的所有`<style/>`标签。捕获它们的样式文本(`innerHTML`)用于确定范围，并将节点附加到上面创建的模板中，用于准备 DOM。
4.  如果没有找到`<style/>`标签，则提前返回。
5.  使用 ShadyCSS 来限定收集的 CSS 文本的范围，并准备模板来应用这些限定了范围的样式。
6.  转发要追加到`<portal-destination/>`元素的非 HTMLStyleElement 元素。

代码如下所示:

```
 get preparedProjected() {
        if (!this.projected) return [];
        if (
             window.ShadyCSS === undefined ||
             window.ShadyCSS.nativeShadow
        ) {
            return this.projected;
        }
        let styles = [];
        let template = document.createElement('template');
        this.projected
            .filter(el => el.constructor === HTMLStyleElement)
            .map((s) => {
                styles.push(s.innerHTML);
                template.appendChild(s);
            });
        if (styles.length === 0) {
            return this.projected;
        }
        template.innerHTML = stylesHTML.join('');
        window.ShadyCSS.ScopingShim.prepareAdoptedCssText(
            styles, this.localName);
        window.ShadyCSS.prepareTemplate(template, this.localName);
        window.ShadyCSS.styleElement(this);
        return this.projected
            .filter(el => el.constructor !== HTMLStyleElement);
    } 
```

这意味着我们的用法说明更容易被接受:

1.  在运行时，父组件不能公开使用`<style/>`元素。
2.  只有作为直接子元素的`<style/>`元素才会应用于“入口”的 light DOM 内容。
3.  `<portal-entrance/>` light DOM 中的`<style/>`元素将直接应用于所有`<portal-destintion/>`元素及其内容，与`name`无关。

> 注意:我希望以这种方式应用的样式能够更好地跟随层叠(看起来它们是以反向层叠的方式添加的)😖，但 ShadyCSS 库的当前状态不一致。我创建了下面这个[问题](https://github.com/webcomponents/polyfills/issues/156)，希望能在期望和现实之间达成某种一致。

经过这些修改，我们的门户元素系列现在可以跨浏览器交付了，无论这些浏览器对影子 DOM 规范的支持程度如何。这种功能带来了一些积极的折衷，但由于它们与 ShadyCSS polyfill 本身带来的功能直接一致，这意味着它们有望为那些使用其他 web 组件和 shadow DOM 工具的人所熟悉。

当您将所有这些都放在我们的 [*菜单的更新版本中时，内容填充菜单*](https://dev.to/westbrook/your-content-in-shadow-dom-portals-3cdb#menu-populates-content-populates-menu) 来自上一篇文章的示例，在其所有跨浏览器支持 glory 中看起来如下:

[https://glitch.com/embed/#!/embed/dirt-fish-1?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/dirt-fish-1?previewSize=100&path=index.html)

<figcaption>近距离观察可能会发现在`<html-include-with-anchors/>`元素上使用了`no-shadow`，升级那里的`<portal-entrance/>`元素存在一些时间问题，我将不得不单独跟进。</figcaption>

从这个基线开始，我们现在可以专注于完善门户的一些功能。

# 声明式 API

无需任何特殊的 API 就能动态跟踪元素的属性，这无疑是定制元素规范最明显的优势之一。通过使用静态的`observedAttributes`数组和相关的`attributeChangedCallback`,我们能够对组件如何对直接在描述它们的标记中声明的变化做出反应进行细粒度的控制。这意味着下面的代码允许我们新定义的定制元素对`custom-attribute`属性的值的变化做出反应，并将该值存储为本地属性。

```
 class DeclarativeElement extends HTMLElement {
        static observedAttributes = ['custom-attribute'];
        attributeChangedCallback(name, oldValue, newValue) {
            switch (name) {
                case 'custom-attribute':
                    this.customProperty = newValue;
                    break;
            }
        }
    } 
```

[其他人](https://dev.to/richharris/why-i-don-t-use-web-components-2cia#6-confusion-between-props-and-attributes)之前已经指出，以这种方式管理*你所有的*属性以及它们与属性的关系可能会相当烦人，我同意这一点。在开发 web 组件时，使用库和工具的一个重要原因是，不必手动将定制元素的 HTML 中要跟踪的所有内容一次一个地连接到相关属性。幸运的是，我们已经承诺使用`LitElement`作为基类，通过它的`static get properties()` API 帮助我们建立这种关系。让我们来看看上面是如何实现的:

```
 class DeclarativeElement extends LitElement {
        static properties = {
            customProperty: {
                type: String,
                attribute: 'custom-attribute'
            }
        }
    } 
```

注意我们的类扩展从`HTMLElement`到`LitElement`的变化。这种变化使我们能够访问一个静态属性 getter，它将概括出我们希望听到的属性变化，我们还会收到一个[扩展选项列表](https://lit-element.polymer-project.org/guide/properties)，您可以用它概括出属性及其相关属性之间的关系。对于我们的`<portal-entrace/>`元素，我们可以概述一个更具声明性的 API，如下所示:

```
 class PortalEntrance extends LitElement {
        static properties = {
            destination: {
                type: String
            },
            manual: {
                type: Boolean
            },
            open: {
                type: Boolean,
                reflect: true
            },
            order: {
                type: Number
            },
        }
    } 
```

以这种方式向基于`LitElement`的定制元素添加属性也意味着对这些属性的更改将自动启动组件的更新生命周期。如果在构建元素的 DOM 表示时使用这些属性，这将非常有帮助。然而，由于这些属性都不需要触发新的渲染，因此有几种方法可以优化这些属性的反应式管理。我们可以扩展这些定义来包含`hasChanged() { return false; }`并完全避免它。或者，我们可以单独使用 [`shouldUpdate`](https://lit-element.polymer-project.org/guide/lifecycle#shouldupdate) 生命周期方法来防止跨组件的整体性。此外，我们知道理解`<slot @slotchange=${this.shouldProjectSlot}></slot>`元素的模板不需要任何处理，我们可以依靠`LitElement`底层的渲染器 [`lit-html`](https://lit-html.polymer-project.org/) ，高效地发现在任何这些更改之后都不需要进行 DOM 更改，并且根本不用担心扩展配置。如此多的选择确保了更高性能的应用程序！为了确保我们的`<portal-entrance/>`元素被渲染一次，然后不再被担心，我们将像这样配对`shouldUpdate`和`firstUpdated`生命周期方法:

```
 shouldRender() {
        return !this._hasRendered;
    }
    firstUpdated() {
        this._hasRendered = true;
    } 
```

这里，我们的第一次更新不受阻碍，但是通过设置`this.shouldRender() = false`作为第一次更新的一部分，没有对渲染阴影 DOM 进行进一步的更新。

现在你可能会问，“如果它们不触发渲染，这些属性*甚至*会做什么？”，而且有充分的理由！首先，让我们记住，与我们的门户相关的所有 DOM 都是以轻量级 DOM 的形式提供的，我们在模板中使用`<slot/>`元素来监听内容的变化，以便通过门户发送，这意味着在内部我们只需要呈现一次，如上所示。当轻量级 DOM 内容发生变化时，将调用`shouldProjectSlot()`，这是我们的组件决定如何处理所提供的 DOM 的地方:

```
 shouldProjectSlot() {
        if (!this.open) {
            if (!this.manual) {
                this.open = true;
            }
        } else if (this.manual) {
            this.projectSlot();
        }
    } 
```

从这个事务中得到的最重要的东西是，当`manual === true`和`open === true`时，`projectSlot()`方法将被直接调用，允许放入`<portal-entrance/>`的内容通过门户流动。否则，当`manual === false`、`open`被设置为`true`时，它依赖于下面的 getter/setter 对:

```
 get open() {
        return this._open;
    }
    set open(open) {
        if (this.open === open) return;
        this._open = open;
        if (open) {
            this.setAttribute('open','');
            this.projectSlot();
        } else {
            this.removeAttribute('open');
            this.close();
        }
    } 
```

在这个 setter 中，我们最终调用了这个上下文中的`projectSlot()`,我们只是做了一个短暂的迂回来保持一个有代表性的状态。这使得我们可以担心尽可能少的条目进入投影功能，同时也将`<portal-entrace/>`元素的内部 API 与外部可用的 API 对齐。

我们还将对我们的`<portal-destintion/>`元素的 API 进行声明性更新。这些添加将使我们的静态属性 getter 如下所示:

```
 class PortalDestination extends LitElement {
        static properties = {
            name: {
                type: String
            },
            projected: {
                type: Array
            },
            multiple: {
                type: Boolean
            },
            announces: {
                type: Boolean
            },
            projecting: {
                type: Boolean
            }
        }
    } 
```

我们将在下面更深入地讨论这些增加的功能，但是，在我们继续之前，请注意`projecting`属性。当内容被投射到组件中时，我们将把它与`projecting`属性结合使用，作为设计组件样式的钩子。这纯粹是内部状态的表示，这将有助于防止从外部改变它。虽然像使用下划线前缀或基于`new Symbol()`的属性名这样的技术可以支持这种安全性，但是我们也可以通过只为这个值提供一个 setter 来管理这个现实:

```
 set projecting(projecting) {
        projecting = this.projected.length > 0;
        if (projecting) {
            this.setAttribute('projecting','');
        } else {
            this.removeAttribute('projecting');
        }
    } 
```

在这里，我们接收一个传入的值，然后简单地将其丢弃。目前，除了样式挂钩之外，我看不出还需要这个属性，所以我们甚至不需要在内部缓存它。在`updated()`生命周期方法中，我们将使用`this.projecting = 'update';`来启动这个功能，setter 将管理`projecting`属性的存在。

准备好我们的声明式 API 后，控制`<portal-entrance/>`的`open`状态和`destination`变得非常简单。请看下面的实际操作:

[https://glitch.com/embed/#!/embed/butter-perfume?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/butter-perfume?previewSize=100&path=index.html)

# 多个入口

既然我们在以声明的方式为我们的门户交付 API 方面更加熟练了，那么为额外的特性这样做将会变得越来越容易。我们之前讨论过的一项支持功能是将来自多个`<portal-entrance />`的内容投射到一个 [`<portal-destination/>`](https://dev.to/westbrook/your-content-in-shadow-dom-portals-3cdb#multiple-entrances) 中，这也是声明式 API 的一个优势；最初由[门户 Vue](https://portal-vue.linusb.org/api/portal-target.html#multiple) 项目概述的另一个特性。我们可以给我们的`<portal-destination/>`元素添加一个`multiple`属性，给我们的`<portal-entrance/>`元素添加一个`order`属性。用法可能如下所示:

```
 <portal-entrance 
        destination="mutliple"
        order="1"
    >
        <h1>Second</h1>
    </portal-entrance>
    <portal-entrance 
        destination="mutliple"
        order="0"
    >
        <h1>First</h1>
    </portal-entrance>
    <portal-destination
        multiple
        name="mutliple"
    ></portal-destination> 
```

在上面的例子中，两个`<h1/>`元素都将被发送到`<portal-destination/>`，由于`multiple`的存在，两个元素都将在其中显示。然而，由于那些`<portal-entrance/>`元素的`order`属性中的值，第一个`<h1/>`将被第二个显示，第二个`<h1/>`将被第一个显示。为了实现这一点，我们在“entry”元素的静态属性 getter 中添加了`order`属性:

```
 order: {
        type: Number
    } 
```

有了 API 级别的属性，它就可以通过`portal-open`交付给我们的“destination”元素:

```
 projectSlot() {
        let content = this.portalContent;
        if (!content.length) return;
        this.dispatchEvent(createEvent('portal-open', {
            destination: this.destination,
            content: content,
            entrance: this,
            order: this.order || 0,
        }));
    } 
```

在“目的地”方面，将会有更多需要更改的内容来支持这种添加。在我们进入这些之前，我们需要将新的属性添加到它的属性 getter 中:

```
 multiple: {
        type: Boolean
    } 
```

同样，这允许我们通过`LitElement`直接连接到匹配属性的`attributeChangedCallback`来接收对该属性的更改。有了自定义元素中的可用内容，我们就可以使用它来决定如何响应被监听的各种事件。具体来说，我们将把`updatePortalContent`方法从一个对最近打开/关闭的`<portal-entrance/>`元素的包罗万象的方法改为一个根据`multiple`的值来管理不同内容的方法:

```
 updatePortalContent(e) {
        this.multiple
            ? this.portalContentFromMultiple(e)
            : this.portalContentFromOne(e);
    } 
```

就这么简单，对吧？瑞莱特。

为了支持这两种代码路径，我们将创建一个中间映射来缓存可用内容，然后将它展平为一个数组的数组，以便推入模板。这意味着我们将创建一个由实际的`<portal-entrance/>`元素控制的`new Map()`,内容就是从这个元素交付的。这些值将被构造为一个对象，包含接收到的内容以及来自“入口”元素的订单值:

```
{
    portal-element =>
        {
            content: node[],
            order: number,
        }
} 
```

我们将通过以下方法构建这些数据来响应`portal-open`事件:

```
 cacheByOriginOnOpen(e) {
        if (e.type !== 'portal-open') return;
        this.projectedByOrigin.set(
            e.detail.entrance,
            {
                content: e.detail.content,
                order: e.detail.order,
            }
        );
    } 
```

我们将在我们的`updatePortalContent`功能的`multiple === false`路径中使用这个映射来决定“目的地”当前是否正在从“入口”接收内容，并在向目的地应用新内容之前关闭该入口:

```
 portalContentFromOne(e) {
        if (this.projectedByOrigin.size) {
            this.projectedByOrigin.keys().next().value.open = false;
        }
        this.cacheByOriginOnOpen(e);
        this.projected = e.detail.content || [];
    } 
```

在`multiple === true`路径上，地图将增强我们根据从“入口”传递的`order`属性对内容进行排序的能力，并将地图展平到我们预期的`projected`属性中:

```
 portalContentFromMultiple(e) {
        this.cacheByOriginOnOpen(e);
        const batchProjected = Array.from(
            this.projectedByOrigin.values()
        );
        batchProjected = batchProjected
            .sort((a,b) => a.order - b.order)
            .reduce((acc, projection) => {
                acc.push(projection.content);
                return acc;
            }, []);
        this.projected = batchProjected;
    } 
```

当调度`portal-close`时，我们将使用这个结构来确保只有有问题的内容被返回到结束的`<portal-entrance/>`元素，同时在再次更新门户内容之前从本地缓存中删除该元素:

```
 closePortal = (e) => {
        if (!this.confirmDestination(e)) return;
        this.returnProjectedWhenManual(e);
        this.projectedByOrigin.delete(e.detail.entrance);
        this.updatePortalContent(e);
    }
    returnProjectedWhenManual({detail: {manual, entrance}}) {
        if (!manual) return;
        const projected = this.projectedByOrigin.get(entrance);
        if (!projected) return;
        projected.content.map(el => entrance.appendChild(el));
    } 
```

在一个实际的应用程序中，这可以展示一个多选项目列表，用`<portal-destination/>`扮演确认 UI 的角色，允许它位于页面上的任何地方。在下面的例子中,“selected”列表将直接出现在十个选项的旁边。然而，在 DOM 中，这两个列表位于完全不同的分支中:

[https://glitch.com/embed/#!/embed/wakeful-piano?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/wakeful-piano?previewSize=100&path=index.html)

# 镜像监听

到目前为止，我们一直依赖于当我们的`<portal-entrance/>`元素带着它们的`portal-open`事件到来时，我们的`<portal-destination/>`元素被激活并被命名。再加上我们最近添加的上述`manual`属性，这似乎是两个元素之间相当完整的 API 关系。然而，如果在我们的“目的地”准备好*接收*之前，我们的“入口”准备好*打开*呢？无论是通过一般的运行时现实，还是在完全控制应用程序的加载过程时有意识地应用，您都有可能遇到这样一种情况，即您打算让一个`<portal-destination/>`等待您的`open`和`<portal-entrace/>`，但它却不在那里。为了支持这一点，让我们添加一些功能来“宣布”我们的“destination”元素中名称的存在或更改。这是对我们的元素的声明性 API 的一个很好的补充，我们可以这样做，同时也可以通过向我们的`<portal-destination/>`元素添加一个`announces`属性来使它选择加入。当我们这样做的时候，让我们也让`name`属性反射，这样我们对该值所做的任何更改都将在呈现的 DOM 中表示出来。

```
 name: {
        type: String,
        reflect: true,
    },
    announces: {
        type: Boolean,
    } 
```

有了`LitElement`,我们有几个选项来决定在哪里对我们属性的变化做出反应。在这种情况下，我们可以依靠 [`updated`](https://lit-element.polymer-project.org/guide/lifecycle#updated) 生命周期方法获得我们需要的所有灵活性。在那里，我们将接收到一个映射，该映射由已经更改的值作为键，指向那些属性的先前值。这将允许我们用`changes.has()`测试`announces`或`name`的变化，就像这样:

```
 updated(changes) {
        if (changes.has('announces')) {
            this.shouldAnnounce();
        } else if (
            changes.has('name') &&
            typeof changes.get('name') !== 'undefined'
        ) {
            this.announce();
        }
        this.projecting = 'update';
    } 
```

在`name`改变的情况下，当值被改变时(不是从`undefined`开始设置时)，我们将立即调用`announce()`元素`<portal-destination/>`的存在。当`announces`的值改变时，我们将调用`shouldAnnounce()`，在调用`announce()`之前确认`announces === true`。这个路径也被添加到`connectedCallback`中，这样当元素重新加入 DOM 时，它也会在被配置时宣布自己。

```
 announce() {
        this.dispatchEvent(createEvent('portal-destination', {
            name: this.name,
        }));
    } 
```

如您所见，`announce`方法再次由自定义事件驱动，这一次是`portal-destination`事件。在`<portal-entrance/>`端，我们将使用一个连接到该事件的`document`和`capture`阶段的侦听器来侦听该事件，以便它能够在尽可能少的干扰下做出相应的响应:

```
 connectedCallback() {
        super.connectedCallback();
        document.addEventListener(
            'portal-destination', 
            this.destinationAvailable,
            true
        );
    }
    disconnectedCallback() {
        super.disconnectedCallback();
        document.removeEventListener(
            'portal-destination',
            this.destinationAvailable,
            true
        );
        this.open = false;
    }
    destinationAvailable = (e) => {
        if (e.detail.name === this.destination) {
            this.shouldProjectSlot();
        }
    } 
```

现在我们在传送门的两边监听。我们已经彻底的 API 甚至更加完整，我们进一步扩展了我们可以利用组件管理内容的方式，以及它可以在整个应用程序中显示的方式。虽然预测加载过程的实际情况将如何影响我们的应用程序的性能并不总是容易的，但是在下面的演示中，我人为地延迟了对`<portal-destination/>`元素的`customElements.define()`调用，以便您可以体验这带来了什么。在控制台打开的情况下运行演示，以跟踪延迟的时间:

[https://glitch.com/embed/#!/embed/mewing-character?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/mewing-character?previewSize=100&path=index.html)

# 更有风格

有了我们作为跨浏览器覆盖的一部分添加的对样式应用程序的支持，我们现在可以更好地控制如何对通过门户发送的内容进行样式化。包含在转发给我们的`<portal-destination/>`的`<portal-entrance/>`的子组件中的样式。`<style/>`那些“入口”的标签子元素也被转发到它们被分配的“目的地”，假设当需要 ShadyCSS 时，那些元素是在`<portal-entrance/>`的父元素的 shadow DOM 最初被多面填充后添加的。然而，当使用定制元素和影子 DOM 时，我们可以有更多的可能性来设计我们的 DOM。

有一些更新的方式，比如使用[可构造样式表](https://dev.to/westbrook/why-would-anyone-use-constructible-stylesheets-anyways-19ng)，以及它们带来的即时性能优势。与`adoptedStyleSheet` API 相呼应，当在预定义的风格系统中工作时，它们也打开了一个[扩展的可能性集](https://benfarrell.com/2019/06/03/adopt-a-design-system-inside-your-web-components-with-constructable-stylesheets/)。还有更常见的概念需要解决，如 CSS 自定义属性。

他们为自定义元素的影子 DOM 提供风格桥梁的方式非常强大。然而，当物理地将 DOM 从 DOM 树的一个部分移动到另一个部分时，它可以将内容从级联中取出，而这些自定义属性依赖于级联才能被适当地应用。如果事先不知道这些自定义属性的存在，就很难获得它们，因此很难找到高效的方法来将这些属性与通过门户发送的内容一起移动。这些概念和更多的研究已经成熟，甚至在这篇文章完成之前，专门讨论风格获取和应用的后续文章似乎是合适的。

# 但是，现在呢？

除了简单地移植我们的`<portal-entrance/>`和`<portal-destination/>`元素来扩展`LitElement`基类，我们已经做了很多:

*   准备跨浏览器交付的元素
*   浮出水面的声明式 API
*   增加了在单个“目的地”显示来自`multiple`“入口”的内容的支持
*   在两个元素之间创建了一个双向关系，这样无论哪个先准备好，门户都可以打开

但是，还有很多事情要做！

甚至在开始围绕支持更丰富风格的应用程序生态系统的实验工作之前，最重要的下一步就是增加测试。即使只是为本文开发演示，我也发现了许多边缘案例，需要完全涵盖这些案例才能称这些组件为“生产就绪”。我已经尽了最大努力来填补我所写的漏洞，但是我确信我已经错过了一些东西，并且这篇文章中没有适当地反映出我的更新。关注这两个元素之间的集成点，为了确保未来的添加和重构不会对我们到目前为止所做的功能产生负面影响，还有很多工作要做。为此，我将花一些宝贵的时间在 Web 组件的测试工作流上，然后在门户的另一边进行更多的探索。我不在的时候，尽量不要关上“入口”。