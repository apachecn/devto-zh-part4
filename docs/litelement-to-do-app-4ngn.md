# LitElement 待办事项应用程序

> 原文：<https://dev.to/westbrook/litelement-to-do-app-4ngn>

## 还有，怎么比反应以及 Vue。

> 这是 2018 年 9 月 24 日来自 [Medium](https://medium.com/@westbrook/litelement-to-do-app-1e08a31707a4) 的一篇文章的交叉帖子，利用了我最近决定在我的写作中使用语法的优势(因此，在这里和那里做了一些小编辑)，如果你在那里看到它，感谢再次查看🙇🏽‍♂️，如果这是你第一次来，欢迎你。

[![Yes, you’re right, LitElement needs a logo. What sort of code library gets big without a logo, right?](img/91a8de335f0c2e6d0c8ec6103e9569de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1b8M44UI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6zu5mivf11vd1jzqd6hf.png)

<figcaption>Yes, you’re right, LitElement needs a logo. What sort of code library gets big without a logo, right?</figcaption>

> 编辑:(1/20/19)更新代码示例和 repo，以将`lit-element@2.0.0`和相关更新应用到那里可用的 API。这些变化包括但不限于基于模板的事件监听器的[预建立的`this`上下文](https://github.com/Polymer/lit-element/pull/244)，[对`adoptedStyleSheets`](https://github.com/Polymer/lit-element/pull/401) ，[更新的属性元数据](https://github.com/Polymer/lit-element/pull/369)，以及[默认类型序列化](https://github.com/Polymer/lit-element/pull/356)。

在一个软件工程师的标准周中，你很难避免一两篇好的“这种方法对那种方法”的文章。在 frontend 的世界中，这通常表现为框架或库 X 如何与 y 中的相同内容进行比较。本周我的表现为【Angular 和 React 及其核心语言之间的比较。在其他几周，可能会有三到七篇不同的文章！然而，一周更多的文章并不能确保你在这些文章中找到真正坚实的写作、逻辑或知识。我认为我们助长了一种自我实现的预言，即某件事写得越多，其他人也会写得越多。当你特别关注那些被认为是“主要的”玩家，比如 Angular、React 或 Vue 时，这个循环甚至会更快，几乎到了不需要的地步。

可悲的是，几乎总的来说，写的东西越多，就越难找到关于这个主题的好文章。这就是为什么当你找到书面形式的技术应用程序的质量比较时，它会令人耳目一新，几周前当我收到苏尼尔·桑德胡的[时，我就这么做了，我在 React 和 Vue 中创建了完全相同的应用程序。以下是不同之处。](https://medium.com/javascript-in-plain-english/i-created-the-exact-same-app-in-react-and-vue-here-are-the-differences-e9a1ae8077fd)尽管 Sunil 明确表示他在写作之前主要与 Vue 一起工作，但他的写作不仅避免了明显的偏袒，而且还采取了额外的步骤，不是用寓言的方式而是用真实的代码来比较这两者；代码要足够复杂以达到重点，足够简单以至于读者可以在不投入过多时间的情况下进行分析。更重要的是，作为一名只在 React 应用程序的边缘或演示代码上工作过的工程师，虽然没有写过一行 Vue，但我真的觉得在完成这篇文章后，我对每一个都有了更深入的理解。

毫无疑问，这种关于某个主题的高质量的写作激发了其他人的兴趣；即使只是我，它已经发生了，你现在也是它的一部分！有时这是一个直接的回答，就像“我也有关于这个领域的观点想分享”，但对我来说，在过去的几周里，我可以停止思考，“这是一篇关于 React 和 Vue 的美丽文章，这篇文章对我所依赖的技术做了什么？”作为一个 web 组件的长期创建者，最近又是一个大量投资的 LitElement 的用户，目前正在由 Google 的 Polymer 项目团队进行疯狂的开发，我敏锐地意识到还没有建立一个漂亮的图书馆来存放关于这个主题的文献。今天，你可能甚至不需要一个完整的报摊来存放这个主题的书面作品。这里有一个简短的列表，你可以选择从这里开始:

*   让我们来构建 Web 组件吧！第 1 部分:Benny Powers 的标准，介绍 dev.to 技术的系列文章的第一部分
*   《聚合物的未来》& lit-html 作者 James Garbutt，深入探讨了来自聚合物项目的各种产品如何相互比较
*   Web，它的组件(它们的可重用性)，它的框架，以及它的不足之处。和[生成一些下一代 web 组件](https://medium.com/@westbrook/generating-some-next-generation-web-components-58fadbb2a56a)分别是对这个领域的总体介绍和我的团队如何开始开发新组件的概述。还有一大串由 Serhii Kulykov 维护的[超赞的 lit-html](https://github.com/web-padawan/awesome-lit-html#readme)

然而，这主要集中在内部比较上。因此，从 Sunil 已经与世界分享的伟大工作开始，这里我试图将他在应用程序级别对这些库的水平比较更进一步，并包括对用 LitElement 构建的相同应用程序的分析。

## 为此，我们开始吧！

* * *

[![Let’s assume I rewrite this structure someday.](img/3cc032950b558fa1895995311a6b77a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A-0PZdr9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ezblshs7zbs65wny1erf.png)

<figcaption>Let’s assume I rewrite this structure someday.</figcaption>

在这个应用程序中，文件的结构当然有一些不同。Polymer CLI 不支持 React 和 Vue 应用程序中显示的`src` / `public`区别，至少不是开箱即用的，所以我选择不与它做太多斗争。为了支持这个决定，您将在我们应用程序的顶层看到一个`index.html`文件；这取代了 Vue 应用程序中的`src/main.js`和 React 应用程序中的`src/index.js`作为应用程序的入口点。我已经把它简化了，因为它只是一个演示，但是即使在大多数交付环境中，你也不需要更多的东西:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="theme-color" content="#000000">
    Lit-Element To Do
    <link rel="stylesheet" href="src/index.css" />
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <to-do></to-do>
    <script type="module" src="./src/ToDo.js"></script>
  </body>
</html> 
```

仍然有一些浏览上下文需要[聚合填充](https://www.npmjs.com/package/@webcomponents/webcomponentsjs)、*和*，我喜欢依靠 [`type="module"`到`nomodule`的技巧](https://philipwalton.com/articles/deploying-es2015-code-in-production-today/)来支持在现代浏览器中交付最少量的翻译，但是除此之外，在你的待办事项应用程序的入口点中，你不会想要太多其他的东西。

在我们深入探讨之前，让我们看一看基于 LitElement 的 web 组件可能是什么样子的:

[![Not a whole lot needed when you’re accepting the `deleteItem` method from the parent element.](img/aa2ab73a9cb87ca187e4ebeb42934ac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gz1xgo3j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y47u2rwjbc0dzcrd8qau.png) 

<figcaption>从父元素接受`deleteItem`方法时，不需要全部。</figcaption>

Web 组件可以很容易地采用你在 Vue 中看到的单个文件组件的方法，但是这里我把样式分成了单独的文件。独特的是，您会注意到样式是从 JS 文件而不是 CSS 文件导入的，这是为了使此处应用的导入系统与浏览器中可能的内容更加一致，并利用作为该基类基础的`lit-html`渲染引擎提供的功能。

[![](img/ad7789b9587a5d83ca20086ec8bdfd3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uXVTbaH0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rvtd5bmworydj5lywsw8.png) 

<figcaption>样式导入导出一个带有完整`<style/>`元素的 TemplateResult，用于任何其他 TemplateResult 中的应用。</figcaption>

上面的样式应用于一个`css`模板标签，该标签通过[可构造样式表对象](https://wicg.github.io/construct-stylesheets/)支持这些样式的实现，这些对象允许您的定制元素在自身的多个实例中共享同一个`<style/>`标签。以这种方式应用你的样式将允许更好的性能，因为这个特性在浏览器中变得可用，并且在`LitElement`内部为还没有实现这个 API 的浏览器填充。如果您喜欢单一文件组件的 Vue 方法，那么没有什么可以阻止您将它放在与您的功能和模板相同的文件中。然而，像这样分割代码使得包含的样式升级到共享样式(那些在您的代码库的多个组件中使用的样式)变得非常容易。

# 我们如何描述和变异数据？

```
static get properties() {
  return {
    list: {type: Array},
    todo: {type: String},
  };
}
constructor() {
  super();
  this.list = [
    this.todoItem('clean the house'),
    this.todoItem('buy milk')
  ];
  this.todo = '';
}
todoItem(todo) {
  return {todo}
} 
```

## LitElement 是怎么做到的？

首先，LitElement 扩展了 HTMLElement，这意味着我们每次使用它时都在定制元素。定制元素给你的第一个超能力是访问`static get observedAttribute()`,它允许你概述元素的属性以便观察。当这些属性改变时，`attributeChangedCallback(name, oldValue, newValue)`将被调用，这允许你的元素响应这些改变。使用 LitElement 时，`static get properties()`中监听的属性会自动添加到`static get observedAttribute()`中，该属性的值默认应用于同名属性。如果您想要(或需要)扩展这里的功能，您可以进一步定制每个属性如何与元素的属性相关联，以及如何与元素的呈现相关联。通过向定义对象添加一个`attribute`键，当您不希望所讨论的属性可通过属性设置时，您可以将值设置为`false`，或者提供一个字符串来概括一个单独命名的属性，以观察该属性的值。上面使用了`converter`属性来概述如何序列化被观察属性的值，当`type`属性被设置为`Array`、`Boolean`、`Object`、`Number`、`String`时，它将默认为适当的处理，但是您可以使用一个双向序列化的方法或一个带有`fromAttribute`和`toAttribute`键的对象来概述消费和发布该属性时应该发生的序列化。`reflect`将作为一个布尔值跟踪属性的值是否应该在所有更改时直接发布到属性，并且`hasChanged`允许您准备一个自定义方法来测试属性值的更改是否应该触发元素 DOM 的更新。当没有提供`hasChanged`方法时，这个测试是通过严格的 JS 身份比较进行的，这意味着由 LitElement 作为属性管理的数据可以很好地与不可变的数据库一起使用。这个扩展属性定义可能看起来像:

```
static get properties() {
  return {
    roundedNumber: {
      attribute: 'number',
      converter: {
        fromAttribute: (value) => Math.round(parseFloat(value)),
        toAttribute: (value) => value + '-attr'
      },
      reflect: true,
    },
  };
} 
```

通过[这个小故障](https://glitch.com/edit/#!/precious-icecream?path=index.html:25:61)，你可以自由地在现实生活中看到它的流逝。如此定义时，`this.roundedNumber`的值将遵循一个生命周期，很像下面的伪代码:

```
<my-el                           // the `number` attribute of
  number="5.32-attr"             // <my-el/> is set so we
></my-el>                        // take the value, 5.32-attr
// run fromAttribute method
parseFloat('5.32-attr');         // parseFloat it, 5.32
Math.round(5.32);                // round it, 5
this.roundedNumber = 5;          // store it in `this.roundedNumber`
// CHANGE RECOGNIZED because 5 !== undefined;
// run toAttribute method
5 + '-attr';                     // append '-attr', '5-attr'
this.setAttribute(
  'number',
  '5-attr'
);                               // set it to the attibute 
```

然而，这不是我们需要利用的待办事项应用程序，所以我们应该在未来的帖子中进一步深入探讨。

所有这些在幕后所做的是为每个属性创建一个`getter`和一个`setter`来管理它的值，并在值如您的`hasChanged`方法中概述的那样改变时调用适当的生命周期方法。这意味着你可以直接操纵状态(即`this.name = ‘John’;`)，就像你使用 Vue 一样，但是当不改变数据的身份(`this.list.push({todo:'Does not Mutate Data’}`不会改变数组的身份，这意味着不会触发新的渲染)时，你将无法触发对模板的更新。然而，在您的脏检查中，额外的灵活性根据需要得到支持(即`hasChanged: (newValue, oldValue) => newValue > oldValue`将仅在您的值增加时触发变化，因此`this.demoValue = this.demoValue + 1`将触发变化，但是`this.demoValue = this.demoValue — 1`不会，如果您看到它有好处的话)。您也可以选择编写自己的定制`getters`和`setters`，但是同样...未来邮报。

您还将看到我添加了`todoItem`方法来抽象待办事项的创建。这绝不是 LitElement 特有的，但我觉得它简化和统一了待办事项代码，因为它用于初始化以及创建新的待办事项。

# 我们如何创建新的待办事项？

```
createNewToDoItem() {
  this.list = [
    ...this.list,
    this.todoItem(this.todo)
  ];
  this.todo = '';
} 
```

## LitElement 是怎么做到的？

如果你说的第一句话是“这看起来像是混合了 React 和 Vue 代码来创建一个新的待办事项”，那么你是对的。Vue 提供的直接属性访问在`this.todo = '';`中仍然有效，React 对唯一数组/对象标识的需求也随着`...this.list`的使用而存在，利用 spread 操作符创建一个具有唯一标识的数组，同时仍然包含来自前一个数组的所有数据。通过这种方式，将数据推入 DOM 并从事件中接收数据与 React 应用程序中的情况非常相似，只有一些不同。

```
<input
  type="text"
  .value=${this.todo}
  @input=${this.handleInput}
/> 
```

您会注意到`.value=${this.todo}`语法。这里你可以看到模板将属性`value`设置为 this.todo 的值，这是因为`value`是少数几个不直接同步到`<input/>`元素中同名属性的属性之一。虽然您可以通过仅设置属性来适当地同步`this.todo`的第一个值，但是将来的更改(特别是那些在创建新的待办事项后清除`<input/>`的更改)不会像预期的那样更新 UI。使用*属性* `value`(以及`.value=${...}`语法)而不是*属性*解决了这个问题。

之后，你会看到`@input`语法，它非常接近我们在 Vue 中看到的事件处理。在这里，它只是为`addEventListener('input',...`添加的模板，在这里用来触发管理`this.todo`值的伪双向绑定。当`<input/>`元素上发生`input`事件时，如下触发`handleInput`方法，将`this.todo`的值设置为`<input/>`的值。(注意:这里使用的是`input`事件，而不是`change`事件。这是因为`change`只会在`blur`事件之后触发，这将阻止`Enter`按钮拥有触发“表单”自我实现的数据。)

```
handleInput(e) {
  this.todo = e.target.value;
} 
```

# 我们如何从列表中删除？

```
deleteItem(indexToDelete) {
  this.list = this.list.filter(
    (toDo, index) => index !== indexToDelete
  );
} 
```

## LitElement 是怎么做到的？

`Array.prototype.filter()`非常适合在这种情况下处理数据，因为默认情况下，它会创建一个具有新标识的数组。这里，我们直接将`this.list`的值设置为通过移除`index === indexToDelete`处的项目而创建的过滤数组，并且响应于显示该变化的变化，请求对 DOM 进行新的更新。

为了实现这一点，我们将首先将`deleteItem`方法绑定到数组中项目的`this`和`key`(索引),并将其作为属性传递给显示各个待办事项的`<to-do-item/>`元素。

```
<to-do-item
  item=${item.todo}
  .deleteItem=${this.deleteItem.bind(this, key)}
></to-do-item> 
```

LitElement 版本的第一步是直接从 React 应用程序重构的，而不是一个生成的应用程序，因此显示了其中的大多数技术在 LitElement 上下文中是如何实现的。然而，这种父母/孩子互动的方式有一些现实，我们应该回顾一下。为了不打断围绕这两种相对论的讨论，我把它和*中相似的观点归为一类，或者我们有吗？*下面一节。

# 我们如何传递事件监听器？

```
<button
  class="ToDo-Add"
  @click=${this.createNewToDoItem}
>+</button> 
```

在这里，我们再次看到 Vue 速记语法像处理程序一样将事件推入 React。然而，和以前一样，当模板将`addEventListener`应用于所讨论的元素时，模板中只有最轻微的魔力(只是纯糖)。您还会注意到`keypress`事件也需要整体处理。

```
<input
  type="text"
  @keypress=${this.handleKeyPress}
/> 
```

就像使用 VanillaJS 一样，直接为`e.key === 'Enter'`处理事件。

```
handleKeyPress(e) {
  if (e.target.value !== '') {
    if (e.key === 'Enter') {
      this.createNewToDoItem();
    }
  }
} 
```

# 我们如何将数据传递给一个子组件？

```
<to-do-item
  item=${item.todo}
  .deleteItem=${this.deleteItem.bind(this, key)}
></to-do-item> 
```

对于我们的每个待办事项，我们需要传递`item`和`deleteItem`的值，以准确地扩展 UI 并触发交互功能。在这两个上下文中，我们通过将属性直接与属性配对来简化属性，因此您会认为我们可以将两者直接作为属性来应用。这个想法对于序列化为`String`的`item`来说非常有效，因此很容易从属性转换为特性，但是对于`deleteItem`方法来说，以这种方式传递函数是不好的。这就是为什么您会看到`.deleteItem`语法表示我们将这个值作为属性设置到`<to-do-item/>`元素上，而不是作为属性。我们将在*中讨论这种方法的注意事项，或者我们有它吗？*下面一节。

# 我们如何将数据发送回父组件？

```
<button class="ToDoItem-Delete"
  @click=${this.deleteItem}>-
</button> 
```

因为我们已经将一个绑定的方法传递给了`deleteItem`的值，所以当我们听到删除按钮上的`click`事件时，我们可以直接调用该方法，并在父元素中看到它的副作用。正如我在*中提到的，我们如何从列表中删除？*我们将在*中重温这个概念，还是我们已经有了这个概念？*下面一节。

# 就这样吧！🎉

简而言之，我们回顾了使用 LitElement 的一些核心概念，包括如何添加、删除和更改数据，如何以属性的形式将数据从父节点传递到子节点，以及如何以事件侦听器的形式将数据从子节点发送到父节点。希望在[的帮助下，我在 React 和 Vue 中创建了完全相同的应用。以下是不同之处。](https://medium.com/javascript-in-plain-english/i-created-the-exact-same-app-in-react-and-vue-here-are-the-differences-e9a1ae8077fd)这已经能够给你一个坚实的介绍，当采用相同的应用时，如何比较 LitElement 与 React 或 Vue。然而，正如 Sunil 所说，

> 当然，[这些库]之间还有许多其他的小差异和怪癖，但是希望本文的内容有助于作为理解[每个]框架如何处理东西的一点基础

因此，希望这只是您探索的开始，无论探索将您带到不断增长的 JavaScript 生态系统的哪个部分。

## Github 链接到 LitElement app:

[https://github.com/westbrook/lit-element-todo](https://github.com/westbrook/lit-element-todo)

## Github 链接到 Sunil 的两个原创应用:

[https://github . com sunil-sandhu/vista-全部](https://github.comsunil-sandhu/vue-todo)

[https://github . com sunil-sand Hu/react-todo](https://github.comsunil-sandhu/react-todo)

# 还是我们有？(回顾一些差异的影响)

如果你一直在享受 LitElement to React 和 Vue 的代码比较，请到此为止。可以说，这里的远处是龙。在 React to do 应用程序的外观上构建了 LitElement to do 应用程序后，我想看看这些功能依赖于更常见的 web 组件实践会是什么样子，并且我想在这种密切比较的背景下分享这些功能。

## 复用性上下文

web 组件化背后的部分概念是可重用性。我们希望创建可以在该应用中反复使用的组件，同时也有可能在我们组织内外的其他应用中使用它们。当将此行为视为 Vue 或 React 应用程序的一部分时，其中使用您正在创建的组件的唯一上下文是在 Vue 或 React 应用程序内部，很容易陷入像将方法传递给孩子这样的轻松和有趣的事情中。

```
<to-do-item
  .deleteItem=${this.deleteItem.bind(this, key)}
></to-do-item> 
```

父节点总是在应用程序中，子节点总是在应用程序中，所以这种技术很有意义，并且已经变得很普遍。这太平常了，当有 React 经验的工程师开始考虑 web 组件时，这是我听到的第一个问题，“我如何将方法传递给孩子？”嗯，答案在上面。然而，当您选择这样做时，您就选择了剥夺使用平台的一项超能力，那就是在应用程序之外工作的能力。您在应用程序之外使用`<input/>`元素时遇到过问题吗？好吧，愚蠢的问题。这些问题曾经是访问一下 MDN 就无法解决的吗？然而，这个基于 LitElement 的`<to-do-item/>`元素和 React 应用程序中的等价的`<ToDoItem />`都期望被传递一个方法来调用为`deleteItem`，这意味着没有办法将它们应用到纯 HTML 中，当点击时不会发现它们出错。`<to-do-item></to-do-item>`应该被赋予在这个待办事项应用程序中使用的能力，在另一个待办事项应用程序中，或在任何真正的东西中，其中一个选项是直接在 HTML 中。为了实现这一点，我们想从 Vue to do 应用程序中取出一个页面，松散地耦合我们的项目，而不需要列表。

## 联轴器松动

在重用的上下文之外，将一个方法传递给一个子对象会阻止，一个需要提供方法的子对象本质上创建了一个向上的依赖链，这是我们当前的工具所不能保证的。可以确保子依赖图是静态的，但是我们没有在父依赖图上要求功能的概念。这意味着我们的`<to-do-item/>`组件的实现者必须了解这一事实，并管理部署它的父组件。紧密耦合。Vue to do 应用程序在很大程度上避免了这种情况，它不是调用一个提供的方法，而是在点击删除按钮时发生一个事件`$emit`:

```
<div class=”ToDoItem-Delete” @click=”deleteItem(todo)”>-</div> 
// ...

deleteItem(todo) {
  this.$emit('delete', todo)
} 
```

当然，这需要更多的代码，但是它给我们带来的灵活性是惊人的。下面是应用于基于`<to-do-item/>`的 LitElement 的相同代码:

```
<button
  class="ToDoItem-Delete"
  @click=${this.deleteItem}
>-</button> 
// ...

deleteItem() {
  const event = new CustomEvent('delete', {
    bubbles: true,
    composed: true,
    detail: {todo: this.todo}
  });
  this.dispatchEvent(event);
} 
```

这种方法的另一个好处是，除了直系父母之外，其他人也可以监听事件，这是我无法立即找到关于 Vue 的`$emit`方法的足够文档的。([这个文档](https://vuejs.org/v2/guide/migration.html#dispatch-and-broadcast-replaced)似乎暗示它创建了一个非冒泡事件，但在这个主题上并不明确。)当`bubbles === true`事件将在你的应用程序中冒泡，直到`e.stopPropagation()`被调用，这意味着它也可以在你的应用程序之外被听到。这对于触发深远的副作用和多种副作用是非常强大的，并且在应用程序中或应用程序外的不同级别上有一个直接的调试路径。在[分支`event`分支](https://github.com/Westbrook/lit-element-todo/tree/event)中，看看这在整个应用程序中是什么样子。

## 交货尺寸

在 Sunil 的文章中,`react-scripts`是作为 React to do 应用程序的直接依赖项发布的，其附带的好处之一是,`yarn build`命令指向那些脚本并为生产准备您的代码。相同概念的[由 Vue 版本的应用程序中的`vue-cli-service`驱动。让开发人员的生活变得更轻松的事情不应该妨碍我们用户的易用性，这很好，包括不要将开发环境代码发布到产品中。更好的是，使用该命令可以将 React 应用程序从 388KB(线下)/1.5MB(解析的)开发应用程序减少到 58KB/187KB，这对您的用户来说是一个不错的胜利。更重要的是，我敢肯定构建过程仍然是相当幼稚的，因为它涉及到构建过程，在实际交付到产品之前，还有进一步缩减规模的空间。沿着这些思路，我将`preact-compat`侵入基于`react-scripts`的 webpack 配置，看看它能做什么，它将应用程序移动到 230KB(通过线路)/875.5KB(已解析)，用于开发应用程序，生产应用程序的时钟为 19.6KB/56KB，这是向理想状态的坚实一跃。我期待着我把它带到这里，激励人们在 Preact 中从头开始创建这个应用程序，我希望看到更好的结果！在 Vue 应用程序中，您可以看到一个 1.7MB(通过网络和解析)的开发应用程序(在 Vue 开发服务器上似乎没有 GZIP)被缩减到更小的 44.5KB(通过网络)/142.8KB(解析)。虽然这些都是很好的结果，但通过使用](https://github.com/sunil-sandhu/vue-todo/blob/master/package.json#L16) `polymer build`(由应用程序的 [`polymer.json`](https://github.com/Westbrook/lit-element-todo/blob/master/polymer.json) 配置中的设置提供支持)来接近相同的概念[需要 35.7KB(线下)/77.5KB(已解析)的开发应用程序，并将其转换为生产就绪的 14.1KB/59KB。这意味着 LitElement 应用程序的整个解析大小与 React 应用程序的 over the wire 大小大致相同，over the wire 大小仅为 Vue 应用程序的 1/3，这对您的用户来说都是巨大的胜利。将这些发现与亚历克斯·罗素在](https://github.com/Westbrook/lit-element-todo/blob/master/package.json#L10)[“开发者体验”诱饵-开关](https://infrequently.org/2018/09/the-developer-experience-bait-and-switch/)中概述的观点联系起来是另一篇文章，但是当从对库或框架的技术理解转向在代码中应用该库或框架时，记住这一点非常重要。这些是我们在 3000 美元的 MacBook Pros 上看不到的性能改进，但当在 Lighthouse 上测试应用连接和 CPU 变慢时，你会开始理解这对一个完整的应用程序可能意味着什么。很像高中化学，用这些💯分数，有很多细微差别...

## 反应待办 App

[![React To-Do App Trace](img/175866212525d904ac8224a562931ecc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0LGZfZpJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cvwq5ynh8cwuxnbxh7ze.png) 

<figcaption>在灯塔审计的运行中没有使用 HTTP2，但是总共有 4 个请求，应该不会有太多收获。</figcaption>

## 预览待办 App

[![Preact To-Do App Trace](img/d796f79e0a9bef1fa1f9f3a8d54ae96c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DDpPdh5F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t2yyhtsv39crm13h7kfw.png) 

<figcaption>在灯塔审计的运行中没有使用 HTTP2，但是总共有 4 个请求，应该不会有太多收获。</figcaption>

## 应用程序视图

[![Vue To-Do App Trace](img/612a6453f7a5a91779d848aaf3abd606.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zGsaZlBZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/81v2r0htgl4ipnpm2x4d.png) 

<figcaption>在灯塔审计的运行中没有使用 HTTP2，但是总共有 5 个请求，应该不会有太多收获。</figcaption>

## LitElement 待办 App

[![LitElement To-Do App Trace](img/da54936d03b5c22c587de40302d4a155.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tDpULFF9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/quunziwn2n05ybh05dvz.png) 

<figcaption>在灯塔审计的运行中没有使用 HTTP2，但是总请求数为 1 时，应该不会有太多收获。</figcaption>

是的，你没看错，`LitElement`待办事项应用程序进入 CPU 空闲状态*的速度几乎是 React 或 Vue 应用程序的两倍，在几乎所有被认为对此次审计很重要的指标上取得了类似的结果。当 Preact 作为 react 的替代部署时，它处于一种虚拟状态，这很可能意味着作为默认的构建依赖，它将运行得更小。如果这也清理了审计中看到的一些较长的“第一*”时间，那将会很有趣。这意味着在负载性能方面肯定有更多的研究要做，并指出在管理应用程序 UI 的最佳选择上没有明确的决策。我将保留未来的想法，Preact 必须继续维护自己的组件模型和虚拟 DOM 引擎，而`lit-html`有可能通过待定的[模板实例化](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Template-Instantiation.md)提案在未来的帖子中进一步剥离自己。*