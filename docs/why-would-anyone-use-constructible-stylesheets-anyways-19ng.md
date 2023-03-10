# 不管怎样，为什么有人会使用可构造的样式表呢？

> 原文：<https://dev.to/westbrook/why-would-anyone-use-constructible-stylesheets-anyways-19ng>

> ！！免责声明:以下文章包含了依赖于 API 的例子，这些 API 不再是新的，并且在 Chrome 和 Firefox 中都不受支持*(编辑:7/6/22)* 。请参考他们的 [Chrome 状态页面](https://www.chromestatus.com/features/5394843094220800)了解有关该支持的信息。！！

的确，为什么？

我是认真的，我自己正在努力找出一个可靠的答案，通过阅读这篇文章，你已经不知不觉地自愿支持我这样做。准备好了吗？

## 什么是可构造样式表

哦，你以前没有见过或使用过[可构造的样式表](https://wicg.github.io/construct-stylesheets/)？嗯，这并不奇怪，他们很漂亮[新](https://www.chromestatus.com/features/5394843094220800)。从现在开始，将会有代码样本和演示供您使用，如果您想在浏览器中看到代码，请考虑 Chrome 状态页面中概述的当前支持级别。除此之外，这里有一个[伟大的入门](https://developers.google.com/web/updates/2019/02/constructable-stylesheets)如果你想提前阅读，它也可能对支持我希望在这里引发的对话大有帮助，让每个人都有一点额外的知识。

一般来说，API 的工作方式如下...

```
 const myStyleSheet = new CSSStyleSheet(); 
```

Enter fullscreen mode Exit fullscreen mode

最简单地说，上面是一个可构造的样式表。`new`关键字已经被用来构造一个`CSSStyleSheet`。一旦你建立了这个样式表，你还会得到这个表上的两个新方法。首先是`replaceSync`方法，它允许同步替换工作表描述的样式:

```
 myStyleSheet.replaceSync('h1 { color: green; }'); 
```

Enter fullscreen mode Exit fullscreen mode

第二个是`replace`方法，它再次允许你替换工作表中的所有样式，但是增加了通过`@import`语句使用外部资源的能力，而不仅仅是静态的样式字符串:

```
 myStyleSheet.replace('@import url("styles.css")'); 
```

Enter fullscreen mode Exit fullscreen mode

后者返回一个承诺，允许您处理加载的成功或失败。您还可以继续访问完整的 [CSSStyleSheet](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet) 对象及其更细粒度的操作方法，如`deleteRule`和`insertRule`，以及访问`cssRules`数组来操作其中特定的 [`CSSRule`](https://developer.mozilla.org/en-US/docs/Web/API/CSSRule) 条目。一旦您的样式表在 javascript 范围内可用，这对您有什么好处呢？不多，直到文档或文档片段“采用”样式表。

## 文档采用的样式表

因为网页最普通的部分是`document`，让我们从那里开始吧！通过下面的`document`上的`adoptedStyleSheets` API 来看看可构造的样式表:

[https://stackblitz.com/edit/constructible-stylesheets-document?view=preview](https://stackblitz.com/edit/constructible-stylesheets-document?view=preview)

现在，在切换到上面的代码视图之前，让我们快速回忆一下，如果没有可构造的样式表，这是如何实现的。大致按最不棒的顺序排列:

*   不断向`<head/>`追加新的`<link rel="stylesheet" />`或`<style />`
*   通过元素的`style`属性管理内联样式
*   切换元素的类/id/其他重要选择器
*   以编程方式管理`<style/>`标签的规则
*   [CSS 自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)

它可能读起来有点像编码面试中的答案。首先，我们可以为每一个变化在页面中强行加入新的样式。您可能会就此打住，但是接下来您会考虑看起来可能更直接一些，所以您只需将更改直接写入手边的元素中。这在这种情况下效果很好，但对于应用于多个元素或更多样式的样式来说，效果就不太好了。对于规模，你追求一条阻力最小的道路，并在一个主类/id 之后选择风格。这为您提供了各种样式的单一支点，以及管理多种不同规则的能力，但是，这也意味着如果不管理更多的门，您就无法非常精细地控制打开/关闭或更改什么样式。

如果您要管理更多的门，为什么要进入 DOM 来这样做呢，将这些门移动到一个实际的`<style/>`标记中，并通过`style.sheet.addRule/deleteRule`直接管理规则，这意味着您必须将所有不同的样式变体运送到某个地方，这增加了您的跨线成本，但是您确实可以通过这种方式获得大量的标量和粒度性能。类似地，你可以通过自定义属性和`element.style.setProperty(propertyName, value)`开关将这些门移动到你的 CSS 中，这很有前途，因为它流经你的应用程序并坚持级联，然而当管理许多不同的属性时，这也很难管理。

是的，我们有所有这些改变应用程序风格的方法，但没有一个是完美的，所以我们有了另一个，`document.adoptedStyleSheets = [...]`，这就是你在上面编辑器视图中看到的。通过这个 API，您可以编写一组 CSS 样式表，供文档或文档片段采用。现在是问“为什么有人会用它”的好时机

为什么，的确。在文档级别，这个 API 可能只是在一个拥挤的选项领域中提供更多的选项，在这个领域中，您需要在面对您的特定目标时准确地权衡取舍以做出决定，而不是提交一个可以远远超过其他人的技术。当然还有空间来研究这可能如何给经过时间考验的方法带来坚实的冲击，如 webpack 驱动的 [CSS 模块](https://github.com/css-modules/css-modules)，而不是[标准跟踪的](https://github.com/w3c/webcomponents/issues/759) CSS 模块，这些模块在将自身注入应用程序时，会向`<head/>`中添加大量的`<style/>`元素。像 [Emotion](https://emotion.sh) 和 [Styled Components](https://www.styled-components.com/) 这样的 CSS-in-JS 库已经通过使用`style.sheet/insertRule`等人编辑了样式，从外部很难猜测它们将在哪里或者如何从与这里的平台的更深层次的集成中受益，但是我想通过这个 API 这些方法会有一些小的胜利。如果你广泛使用这些工具，并能看到一些成功，或者如果你使用其他工具，你能看到这些方法的价值，我希望你在下面的评论中分享一些你看到的开放给你的选项！

然而，当这个 API 应用于使用影子 DOM 的元素时，它才开始获得它的晚餐。这样做，你们都有能力并且越来越需要在一个文档中多次应用一个样式表。

## shadowroot . adopted stylesheets

这不仅是规范最初的目标，也是它开始变酷的地方...在我们开始之前，这里有一个关于 Shadow DOM 的快速入门，适合那些可能不会每天都使用它的人。

```
 function createShadow(el) {
        const shadowRoot = el.attachShadow({ mode: "open" });
        shadowRoot.innerHTML = `
            <style>
                h1 {
                    color: red;
                    size: 3em;
                }
            </style>
            <h1>This is in a Shadow Root</h1>
        `;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将一个影子根附加到提供的`el`上，然后是`innerHTML`的一些内容和样式。看起来非常简单，但是在 JS 的行之间，神奇的事情发生了，我们将应用的内容和样式从文档的其余部分封装在一个文档片段中，保护它免受选择器(CSS 和 JS)的窥探，并保护文档的其余部分免受其样式的影响。更重要的是，在我们新的`shadowRoot`变量中，我们创建了另一个可以使用`adoptedStyleSheets` API 的位置。

### 共享

现在，假设您将上述阴影根附加到一个定制元素，并假设您想要将数十个或数百个定制元素放入您的内容中。您正在重用代码，您正在从页面的其余部分封装代码，您对页面性能感觉良好，直到您意识到您现在正在为这些元素中的每一个创建一个新的(理论上是唯一的，尽管有些浏览器会在幕后为您解决这个问题)样式表。仅仅使用一种样式，比如我们的例子，您可能能够承担解析成本，但是想象一下，在阅读本文之前，这个概念与您使用的最后一个样式表相交，您很可能会看到成本越积越多。这就是我们的页面现在不仅仅有一两个位置可以使用`adoptedStyleSheets` API 的原因，而是您创建的定制元素的每个实例都有一个位置开始发挥作用。

```
 const sheet = new CSSStyleSheet();
    sheet.replaceSync(`
        h1 {
            color: red;
            size: 3em;
        }
    `);

    function createShadow(el) {
        const shadowRoot = el.attachShadow({ mode: "open" });
        shadowRoot.innerHTML = `
            <h1>This is in a Shadow Root</h1>
        `;
        shadowRoot.adoptedStyleSheets = [sheet];
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 懒惰

假设这是通过定制元素发布到页面上的，我们可以将这段代码的结构向前推进一步。目前，这个例子只获得了在我们的自定义元素的无数实例之间共享样式表的好处，然而在来自[可构造样式表提案](https://wicg.github.io/construct-stylesheets/#proposed-solution)的主例子的上下文中，我们还可以利用在页面加载时 DOM 不可用的自定义元素的可能性来延迟解析来自共享表的样式:

```
 const myElementSheet = new CSSStyleSheet();
    class MyElement extends HTMLElement {
        constructor() {
            super();
            const shadowRoot = this.attachShadow({ mode: "open" });
            shadowRoot.adoptedStyleSheets = [myElementSheet];
        }

        connectedCallback() {
            // Only actually parse the stylesheet when the first instance is connected.
            if (myElementSheet.cssRules.length == 0) {
                myElementSheet.replaceSync(styleText);
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 作曲

在跨元素共享样式和管理这些样式的解析时间的同时，可构造的样式表也支持通过`adoptedStyleSheets = [...sheets]`进行样式合成。使用影子 DOM 及其提供的封装的主要好处之一是返回到小文档。有争议的是，当社区提出在现代环境中使用 CSS 的困难时，当前的中心问题是，当它被创建时，我们所处理的文档要小得多；小文档、小样式表、少量工作来管理它们的关系。Shadow DOM 在恢复这一点上走了很长的路，现在当与`adoptedStyleSheets`混合时，它可以被带到下一个级别。

比起通过 CSS 编译来处理样式系统的应用，你可以静态地构建样式，a la:

```
 @import 'colors';
    @import 'headlines';
    @import 'layout';
    @import 'lists';
    // etc. 
```

Enter fullscreen mode Exit fullscreen mode

然后将这些风格应用到你的网站上，比如:

```
 <link rel="stylesheet" href="all-my-styles.css" /> 
```

Enter fullscreen mode Exit fullscreen mode

一个自定义元素现在可以共享*和*将你的样式系统的片段组合成它自己，这种方式可以进一步减轻死 CSS 移除的痛苦，这是通过以下方式实现的:

```
 import {
        colorsSheet,
        headlinesSheet,
        listsSheet,
    } from '/style-system.js';
    import {
        styles,
    } from './styles.js';

    // ...

    connectedCallback() {
            // Only compose styles once
            if (this.shadowRoot.adoptedStyleSheets.length == 0) {
                this.shadowRoot.adoptedStyleSheet = [
                    colorSheet,
                    headlinesSheet,
                    listsSheet,
                    styles,
                ];
            }
        } 
```

Enter fullscreen mode Exit fullscreen mode

如果您的组件商店中有列表，那么移除`listsSheet`导入，当您的构建中没有组件有列表时，样式表将简单地从您的构建中移除。当[原生 CSS 模块](https://github.com/w3c/webcomponents/issues/759)通过标准过程，我们可以开始依赖类似
的代码时，这就更好了

```
 import styles from './styles.css';

    const sheet = new CSSStyleSheet();
    sheet.replace(styles); 
```

Enter fullscreen mode Exit fullscreen mode

这解决了一个我在本文中一直回避的重要现实。这是一个 JS API，这意味着我们正在讨论在字符串中使用我们的样式。如果没有像原生 CSS 模块这样的东西允许我们的代码以字符串形式直接访问实际 CSS 文件中的样式，在运行时或[构建时]没有特殊处理，那么这些样式将需要存在于 JS 中。你可以隐藏在你没有*修改*那些样式的事实后面(尽管你当然可以),说这个过程不是 CSS-in-JS。然而，这主要是一个用于管理 CSS 的 JS API，所以人们有理由称这种区别为技术上的。无论哪种方式，可构造样式表的人体工程学使你在一个它们不能成功地与 CSS 模块配对的世界里有所欠缺。希望 [JSON 模块](https://github.com/w3ctag/design-reviews/issues/375)在规范层面的成功能够重新点燃 CSS 领域的进步。

## 所以，为什么？

既然我们都知道了如何使用可构造的样式表，以及它们能做什么，问题仍然是“为什么有人会使用它们？”。或者，也许是，“为什么*你*会使用它们？”希望通过上面讨论的所有介绍、可能性和技术，你已经开始对它们可能在你的工作中提供的东西有所感觉。如果是这样，我想在下面的评论中听到它。下面是我们在上面讨论过的优势的回顾，以此开始对话:

*   风格共享对于性能来说，少即是多，取决于你当前的风格应用技术，一个`adoptedStyleSheets`界面可以为你节省数十或数百个`<style/>`元素，不管你是否使用 Shadow DOM。
*   懒散地解析和应用样式允许我们在组件化环境中没有机会利用的控制级别
*   样式组合允许更精确地应用样式，也允许在删除样式时保持同样的精度，这意味着您将比以往任何时候都更容易确保只向用户发送所需的内容
*   还有，更多...

没错，还有更多，一些使用这个 API 的好理由已经开始以好的博客帖子、库和规范提案的形式出现。它们都值得一试，但我在下面收集了一些样本。

### 样式系统应用

在他的文章[中，采用带有可构造样式表的 web 组件内部的设计系统](https://benfarrell.com/2019/06/03/adopt-a-design-system-inside-your-web-components-with-constructable-stylesheets/)， [Ben Ferrel](https://twitter.com/bfarrellforever) 讨论了如何采用预先存在的样式系统，并将其应用到 Web 组件中，而不必为上下文重写。在这里，他是在 Adobe 的 Spectrum UI 系统的范围内完成的，但这项工作是一个坚实的概念证明，告诉你如何为 Bootstrap 或 Tailwind CSS 之类的东西做同样的事情。在 Shadow DOM 中应用这些系统对于工程师来说是一个早期的阻碍，因为他们开始发现定制元素在现代开发中所扮演的角色，这种模式可以打开在更广泛的环境中使用 web 组件的大门。在他的文章中，Ben 甚至快速回顾了在已经本地支持 Shadow DOM 的浏览器中聚合填充`adoptedStyleSheets` API 的可能性。

### 标准库元素

当我第一次接触 web 组件规范时，扩展开发人员可用的 HTML 元素标准库的可能性是最令人兴奋的概念之一。随着他们的巩固和对他们的支持不断增长，这个梦想终于开始成为现实。随着的推出， [WHATWG](https://whatwg.org/) 在深度集成的层面上打开了选择加入 HTML 元素扩展的大门。这个提议中我最喜欢的特性之一是`--std-control-theme`一个 CSS 自定义属性，当 JS [读取该属性时，它会有条件地采用](https://github.com/tkent-google/std-switch/blob/master/src/switch.mjs#L474)描述该主题的样式表。这是在 HTML、CSS 和 JS 的自然交叉点上对声明式样式的令人印象深刻的使用，我希望在未来能看到更多。

### 灵活基类

我第一次了解到可构造的样式表是由 [LitElement](https://lit-element.polymer-project.org/guide/styles#static-styles) web 组件基类提供的 API 的一部分。当依赖于它的`static get styles`访问器时，LitElement 应用了一点优雅的降级，以允许在可用时使用可构造的样式表。该功能旨在使 [Shadow DOM](https://developers.google.com/web/fundamentals/web-components/shadowdom) 的 CSS 上下文中的样式组合更加容易，甚至更具性能，这是该规范的明显优势。LitElement 和它对可构造样式表的使用都是值得扩展的主题。

## 接下来是什么？

由于可构造的样式表仍然是如此的新，我们作为一个技术社区才刚刚开始接触到使用它们的可能性。甚至在这篇开始寻找“为什么”有人会使用它们的文章中，我也提出了更多我自己已经回答过的问题。我提到了通过 LitElement 基类对这个概念的介绍，我想写更多关于它的样子，以及它的局限性。最重要的是，我期待着分享一些我认为随着工作流中某些限制(IE11/pre-Edgeium Edge 支持)的解除而可能实现的东西。为此，我想给你们看下面的演示:

[https://stackblitz.com/edit/constructible-stylesheets?view=preview&file=cs-element.js](https://stackblitz.com/edit/constructible-stylesheets?view=preview&file=cs-element.js)

以上扩展了这样一种思想，即您可以拥有多个相同的定制元素，每个元素都有一个特定于其自身的样式应用程序，方法是允许特色定制元素在它所在的应用程序的上下文中解析这些依赖关系。同样，我认为自定义元素可以采取不同的关系将样式应用于其轻量级 DOM 内容。期待这些想法和更多更深入的讨论，以及一个不那么做作的例子！