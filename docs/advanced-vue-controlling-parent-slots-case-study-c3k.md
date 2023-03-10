# 高级 Vue:控制父插槽(案例研究)

> 原文：<https://dev.to/michaelthiessen/advanced-vue-controlling-parent-slots-case-study-c3k>

让我问你一些你可能从未想过的事情:

> 有办法从子组件填充父组件的插槽吗？

最近一位同事问我这个问题，简短的回答是:

是的。

但是我得出的解决方案可能与你现在所想的非常不同。

你看，我的第一个方法被证明是一个糟糕的想法，在我找到我认为解决这个问题的最佳方法之前，我尝试了几次。

这是一个棘手的 Vue 架构问题，但也是一个非常有趣的问题。

在本文中，我们将逐一介绍这些解决方案，并了解为什么我认为它们没有那么好。最终，我们会找到最佳解决方案。

但是为什么我们一开始会有这个问题呢？

## 为什么这个问题晦涩难懂？

[![](img/60ec6a531d5de72d84b3ce9cab4d9602.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oHoMRkeo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://michaelnthiessen.com/assets/static/screenshot.2665e34.726cb87.png)

在我们的应用程序中，我们有一个包含不同按钮的顶栏、一个搜索栏和一些其他控件。

根据您所在的页面，它可能会略有不同，因此我们需要一种基于每个页面的配置方法。

[![](img/4d06684deb80a79d4e32557401be9375.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Nef2R1O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://michaelnthiessen.com/assets/static/screenshot2.2665e34.5c8f699.png)

为此，我们希望每个页面都能够配置动作栏。

看起来很简单，但是有个问题:

这个顶栏(我们称之为`ActionBar`)实际上是我们主布局脚手架的一部分，看起来像这样:

```
<template>
  <div>
    <FullPageError />
    <ActionBar />
    <App />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

其中`App`是基于您所在的页面/路径动态注入的。

`ActionBar`有一些我们可以用来配置它的插槽。但是我们如何从`App`组件控制这些插槽呢？

## 定义问题

首先，尽可能清楚我们到底要解决什么是个好主意。

让我们看一个有一个子组件和一个插槽的组件:

```
// Parent.vue
<template>
  <div>
    <Child />
    <slot />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样填充`Parent`的槽:

```
// App.vue
<template>
  <Parent>
    <p>This content goes into the slot</p>
  </Parent>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的...

填充子组件的插槽很容易，这就是插槽通常被使用的方式。

但是有没有一种方法可以让我们从`Child`组件内部控制进入`Parent`组件的`slot`的内容呢？

更一般地说:

> 我们能让一个子组件填充父组件的插槽吗？

让我们来看看我想到的第一个解决方案。

## 道具下降，事件上升

我对这个问题的第一反应是一个我不断想起的咒语:

> 道具下降，事件上升

数据在组件树中向下流动的唯一方式是使用 *props* 。你与树中其他人交流的唯一方式是发射*事件*。

这意味着如果我们需要从一个孩子到一个父母的交流，我们使用事件。

所以我们将使用事件将内容传递到`ActionBar`的插槽中！

在每个应用程序组件中，我们需要做以下事情:

```
import SlotContent from './SlotContent';

export default {
  name: 'Application',
  created() {
    // As soon as this component is created we'll emit our events
    this.$emit('slot-content', SlotContent);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将任何我们想放入槽中的东西打包成一个`SlotContent`组件(名字并不重要)。一旦创建了应用程序组件，我们就发出`slot-content`事件，传递我们想要使用的组件。

我们的支架组件看起来应该是这样的:

```
<template>
  <div>
    <FullPageError />
    <ActionBar>
      <Component :is="slotContent" />
    </ActionBar>
    <App @slot-content="component => slotContent = component" />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

它将监听该事件，并将`slotContent`设置为我们的`App`组件发送给我们的值。然后，使用内置的`Component`，我们可以动态地呈现该组件。

尽管传递带有事件的组件感觉怪怪的，因为这并不是我们的应用程序中真正“发生”的事情。这只是应用程序设计方式的一部分。

幸运的是，有一种方法可以让我们完全避免使用事件。

## 寻找其他$选项

由于 Vue 组件只是 Javascript 对象，我们可以给它们添加任何我们想要的属性。

我们可以不使用事件来传递插槽内容，而是将它作为一个字段添加到我们的组件:

```
import SlotContent from './SlotContent';

export default {
  name: 'Application',
  slotContent: SlotContent,
  props: { /***/ },
  computed: { /***/ },
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们必须稍微改变一下在我们的搭建中访问这个组件的方式:

```
<template>
  <div>
    <FullPageError />
    <ActionBar>
      <Component :is="slotContent" />
    </ActionBar>
    <App />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

```
import App from './App';
import FullPageError from './FullPageError';
import ActionBar from './ActionBar';

export default {
  name: 'Scaffold',
  components: {
    App,
    FullPageError,
    ActionBar,
  }
  data() {
    return {
      slotContent: App.slotContent,
    }
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

这更像静态配置，更好也更干净👌

但这还是不对。

理想情况下，我们不会在代码中混合范例，并且*所有的*都是以声明的方式完成的。

但是在这里，我们不是将组件组合在一起，而是将它们作为 Javascript 对象传递。

如果我们能以一种正常的 Vue 方式写下我们想要出现在插槽中的内容就好了。

## 思维在门户

这就是门户的用武之地。

它们的工作方式完全符合您的预期。你可以把任何东西从一个地方传送到另一个地方。在我们的例子中，我们将元素从 DOM 中的一个位置“传送”到另一个位置。

我们能够控制组件在 DOM 中的呈现位置，而不管组件树是什么样子。

例如，假设我们想要填充一个模态。但是我们的模态必须呈现在页面的根目录，这样我们才能正确地覆盖它。首先我们要在模态中指定我们想要的:

```
<template>
  <div>
    <!-- Other components -->
    <Portal to="modal">
      Rendered in the modal.
    </Portal>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的模态组件中，我们将有另一个门户来呈现内容:

```
<template>
  <div>
    <h1>Modal</h1>
    <Portal from="modal" />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

这当然是一个进步，因为现在我们实际上是在编写 HTML，而不仅仅是传递对象。它的声明性更强，也更容易看到应用程序中发生了什么。

除了在某些方面*不*更容易看到发生了什么。

因为门户在不同的地方渲染元素，所以它完全打破了 Vue 中 DOM 渲染的模式。看起来你在正常的渲染元素，但是根本不能正常工作。这可能会导致许多困惑和沮丧。

这还有另一个大问题，但是我们稍后会谈到。

至少通过将组件添加到`$options`属性，很明显您正在做一些不同的事情。

我认为还有更好的方法。

## 解除状态

“提升状态”是前端开发界经常使用的一个术语。

这意味着您将状态从子组件移动到父组件或祖父组件。你把它移到组件树上。

这可能会对应用程序的架构产生深远的影响。就我们的目的而言，它实际上打开了一个完全不同的—更简单的—解决方案。

我们这里的“状态”是我们试图传递到`ActionBar`组件的槽中的内容。

但是该状态包含在`Page`组件中，我们不能真正将页面特定的逻辑移动到布局组件中。我们的状态必须保持在我们动态呈现的那个`Page`组件中。

因此，为了提升状态，我们必须提升整个`Page`组件。

目前我们的`Page`组件是`Layout`组件的子组件:

```
<template>
  <div>
    <FullPageError />
    <ActionBar />
    <Page />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

提升它需要我们翻转它，使`Layout`组件成为`Page`组件的子组件。我们的`Page`组件应该是这样的:

```
<template>
  <Layout>
    <!-- Page-specific content -->
  </Layout>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我们的`Layout`组件现在看起来像这样，我们可以使用一个插槽来插入页面内容:

```
<template>
  <div>
    <FullPageError />
    <ActionBar />
    <slot />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

但是这还不允许我们定制任何东西。我们必须在我们的`Layout`组件中添加一些命名槽，这样我们就可以传入应该放入`ActionBar`的内容。

最直接的方法是用一个插槽完全取代`ActionBar`组件:

```
<template>
  <div>
    <FullPageError />
    <slot name="actionbar">
      <ActionBar />
    </slot>
    <slot />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

这样，如果你不指定“actionbar”槽，我们就得到默认的`ActionBar`组件。但是您仍然可以用您自己的定制`ActionBar`配置:
来覆盖这个插槽

```
<template>
  <Layout>
    <template #actionbar>
      <ActionBar>
        <!-- Custom content that goes into the action bar -->
      </ActionBar>
    </template>
    <!-- Page-specific content -->
  </Layout>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这是做事情的理想方式，但是它确实需要你重构你的页面布局。这可能是一项艰巨的任务，取决于你的应用程序是如何构建的。

如果您不能使用这个方法，我的下一个首选方法可能是#2，使用`$options`属性。这是最干净的，也是任何阅读代码的人最容易理解的。

## 我们可以让这变得更简单

当我们第一次定义这个问题时，我们以更一般的形式表述它，如下所示:

> 我们能让一个子组件填充父组件的插槽吗？

但真的，这个问题和道具具体没什么关系。更简单地说，就是让一个子组件控制在它自己的子树之外呈现什么。

在最一般的形式中，我们会这样陈述这个问题:

> 对于一个组件来说，控制在它的子树之外呈现什么的最好方法是什么？

从这个角度审视我们提出的每一个解决方案，给了我们一个有趣的新视角。

### 向上一级父级发出事件

因为我们的组件不能直接影响它的子树之外发生的事情，所以我们找到一个组件，它的子树包含我们试图控制的目标元素。

然后我们请求它为我们改变它。

### 静态配置

我们不是主动要求另一个组件代表我们做一些事情，而是简单地将必要的信息提供给其他组件。

### 门户网站

你可能会注意到前三种方法中的一种模式。

所以让我做出这样的断言:

组件没有办法控制它的子树之外的东西。

(证明这是留给读者的一个练习)

因此，这里的每种方法都是让另一个组件执行我们的命令，并控制我们真正感兴趣的元素的不同方式。

门户在这方面更好的原因是它们允许我们将所有的通信逻辑封装到单独的组件中。

### 解除状态

这是事情真正开始改变的地方，也是为什么提升状态是一个比我们看到的前 3 个更简单和更强大的技术。

这里我们的主要限制是我们想要控制的东西在我们的子树之外。

对此最简单的解决方案是:

将目标元素移到我们的子树中，这样我们就可以控制它了！

提升状态——以及操作该状态的逻辑——允许我们拥有一个更大的子树，并在该子树中包含我们的目标元素。

如果你能做到这一点，这是解决这个特定问题以及一整类相关问题的最简单的方法。

请记住，这并不一定意味着提升整个组件。您还可以重构您的应用程序，将一段逻辑移到树中更高位置的组件中。

## 其实只是依赖注入

你们中一些更熟悉软件工程设计模式的人可能已经注意到，我们在这里做的是依赖注入——一种我们在软件工程中已经使用了几十年的技术。

它的用途之一是编写易于配置的代码。在我们的例子中，我们在每个使用组件的`Page`中配置不同的`Layout`组件。

当我们翻转`Page`和`Layout`组件时，我们正在做所谓的控制反转。

在基于组件的框架中，父组件控制子组件做什么(因为它在子树中)，所以我们没有让`Layout`组件控制`Page`，而是选择让`Page`组件控制`Layout`组件。

为了做到这一点，我们使用插槽向`Layout`组件提供完成工作所需的东西。

正如我们已经看到的，使用依赖注入的效果是使我们的代码更加模块化，更容易配置。

## 结论

我们经历了解决这个问题的 4 种不同方法，展示了每种解决方案的优点和缺点。然后我们更进一步，将问题转化为一个更一般的问题，即控制组件子树之外的东西。

我希望您会看到提升状态和依赖注入是两种非常有用的模式。它们是你的武器库中拥有的极好的工具，因为它们可以应用于无数的软件开发问题。

但最重要的是，我希望你把这个拿走:

通过使用一些常见的软件模式，我们能够将一个只有丑陋解决方案的问题变成一个有着非常优雅解决方案的问题。

许多其他问题也可以用这种方式解决——把一个丑陋、复杂的问题转化成一个更简单、更容易解决的问题。

如果您想要一些关于插槽的更高级的内容，我[复制了 v-for 指令](https://michaelnthiessen.com/nested-slots-in-vue)，展示了如何递归地使用嵌套插槽和嵌套作用域插槽。这是我最喜欢的文章之一，所以一定要看看！