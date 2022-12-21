# Vue.js 作用域插槽

> 原文：<https://dev.to/bagwaa/vue-js-scoped-slots-3ej3>

当作用域插槽第一次被引入到 [Vue.js](https://vuejs.org/) 中时，我发现完全理解它们很有挑战性，与其说是语法，倒不如说是何时以及为什么我会使用它们。

这是因为对于作用域插槽来说，更重要的是学习概念而不是语法，所以记住这一点，让我们更深入地研究作用域插槽，我们将从常规插槽开始，以确保我们都在同一页上，然后转移到作用域插槽试图解决的问题。

*本文介绍了在版本 2.6.0 中引入的使用 v-slot 的新作用域 slot 语法，使用 slot 和 slot-scope 属性的旧语法现在已被弃用，但在本文发布时仍可在框架中使用。*

[https://www.youtube.com/embed/fauAWw6SVTY](https://www.youtube.com/embed/fauAWw6SVTY)

我将在本文的最后介绍旧的语法，因为它仍然被许多开发者广泛使用，并且在自然界中有很多这样的例子。

## 什么是规则槽？

让我们快速介绍一下槽以及如何在 Vue 中使用它们，常规槽只是将数据传递到组件的一种方式，如果你已经知道如何使用 props，那么将数据传递到另一个组件的想法应该很熟悉，让我们来看看这两种不同的方法。

从消费者的角度来看，使用 props 将数据传递到组件看起来是这样的。

```
<Comment content="Im too old to play Mario Maker 2"> 
```

道具很棒，但是当你需要将数据传递给一个组件，而不是文本或对象时，道具就没那么有用了，主要是因为通过道具传递 html 会很快变得一团糟。

相反，如果我们为了语义和样式的目的想要传入一些 html，那么我们可以以下面的方式使用一个常规的 slot。

```
<template>
  <div id="app">
    <Comment>
      <span class="text-center text-green-400">
        Making levels in Mario Maker 2 is Addictive!
      </span>
    </Comment>
  </div> </template> 
<script>
import Comment from './components/Comment'

export default {
  name: 'app',
  components: {
    Comment
  }
}
</script> 
```

在上面的例子中，你可以看到我们不仅使用常规的默认槽来传入文本，而且我们还传入了一个额外的 wrapping span 标签，该标签应用了一些 [tailwindcss](https://tailwindcss.com/) 样式。

开始和结束标签之间的内容将被传递给子标签，并替换组件内的`<slot></slot>`标签。

在这种情况下，插槽的最大优势是组件的消费者可以选择如何设置文本样式，因为它们还负责传递换行。

在 Vue.js 中定义一个带有 slot 的组件非常简单，现在我们已经看到了如何使用它，让我们快速地看一下如何定义这个非常简单的示例组件。

```
<template>
  <div>
    <div>the following comment was left</div>
    <slot></slot>
  </div> </template> 
<script>
export default {
  name: 'Comment',
}
</script> 
```

我知道在这个例子中，输出很大程度上是多余的和无意义的，但是我在这里尽可能保持简单，只是为了尝试和解释这个概念，而不是陷入 html 或组件的页面。

这很好，但是我们有一个作用域的问题，当我们把任何东西从父组件传递到子组件时，它在父组件中被求值，所以如果我们想访问存储在子组件状态中的值，那么我们将不能，因为我们将只计算父组件中的所有表达式。

我知道这听起来有点令人困惑，所以让我们看另一个例子来看看这个问题是如何发生的。

## 作用域插槽试图解决哪些问题？

让我们用几个例子来定义这个问题，首先，让我们创建一个组件来列出我们可能从 API 获取的一些数据，某种类型的`<product-listing>`组件。

```
<template>
  <div class="products">
    <h1>A List of Nintendo Games</h1>
    <div>
      <div v-for="product in products" :key="product.id">
        <span class="product_name">{{ product.name }}</span>
        <span class="product_price">{{ product.price }}</span>
      </div>
    </div>
  </div> </template> 
<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: "Mario Maker 2", rating: 4, price: 40000 },
        { id: 2, name: "Breath of the Wild", rating: 5, price: 60000 },
        { id: 3, name: "Tetris 99", rating: 4, price: 8000 }
      ]
    }
  }
}
</script> 
```

接下来，让我们看看使用这个组件的代码，以及我们如何使用它来显示任天堂游戏的基本列表。

```
<template>
  <div id="app">
    <product-listing></product-listing>
  </div> </template> 
<script>
import ProductListing from './components/ProductListing'

export default {
  name: 'app',
  components: {
    ProductListing
  }
}
</script> 
```

这很好，但是主要的问题是我们没有办法改变子组件显示的信息或者它在浏览器中的呈现方式。

例如，我们可能决定，作为这个组件的消费者，我们希望显示游戏的等级，我们不想改变实际的组件本身，因为这种行为可能并不总是必要的。

这是我们可能接触到普通插槽的地方，让我们重构这个例子，传入我们想要显示的内容，并用一个`<slot>`
替换当前的静态布局

```
<template>
  <div class="products">
    <h1>A List of Nintendo Games</h1>
    <div>
      <div v-for="product in products" :key="product.id">
        <slot></slot>
      </div>
    </div>
  </div> </template> 
<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: "Mario Maker 2", rating: 4, price: 40000 },
        { id: 2, name: "Breath of the Wild", rating: 5, price: 60000 },
        { id: 3, name: "Tetris 99", rating: 4, price: 8000 }
      ]
    }
  }
}
</script> 
```

请注意，我们已经删除了产品列表的内容，并用一个我们想放入的位置替换了它，然后我们可能会尝试像这样使用这个组件。

```
<template>
  <div id="app">
    <product-listing>
      <span class="product_name">{{ product.name }}</span>
      <span class="product_rating">{{ product.rating }}</span>
      <span class="product_price">{{ product.price }}</span>
    </product-listing>
  </div> </template> 
<script>
import ProductListing from './components/ProductListing'

export default {
  name: 'app',
  components: {
    ProductListing
  }
}
</script> 
```

当我们从父组件调用这个组件时，您可以看到我们传入了 span 标签以及我们想要显示的数据，请注意我们是如何传入我们想要在此显示的额外数据的，即产品评级。

虽然这看起来很好，但遗憾的是它不会工作，如果我们看看 chrome 开发工具的内部，我们会看到一个类似这样的错误。

> 属性或方法“product”未在实例上定义，但在呈现期间被引用。

[![Error Example](img/c69e0805e19b28b75e0cdf47dd2b5dbc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pi5xt67---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./error.png)

这里的问题是，在父组件中，我们试图传递名称、等级和价格，这些都存储在产品中，而父组件对产品的数据一无所知，因为这仅限于子组件。

我们需要的是一种方法，使我们的子组件的作用域在开始和结束的`<product-listing>`标记之间对我们的父组件可用，这正是作用域插槽所提供的。

## 如何使用作用域槽

让我们看一下重构后的代码，利用作用域槽来解决上面概述的问题，首先，我们的组件需要使其当前作用域中的某些东西对其父组件可用，在我们的例子中，这是一个产品。

```
<template>
  <div class="products">
    <h1>A List of Nintendo Games</h1>
    <div>
      <div v-for="product in products" :key="product.id">
        <slot :product="product"></slot>
      </div>
    </div>
  </div> </template> 
<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: "Mario Maker 2", rating: 4, price: 40000 },
        { id: 2, name: "Breath of the Wild", rating: 5, price: 60000 },
        { id: 3, name: "Tetris 99", rating: 4, price: 8000 }
      ]
    }
  }
}
</script> 
```

这里最重要的一行是我们定义实际插槽的地方，您会注意到我们现在已经将产品绑定到实际插槽标签，使其可供我们的父项使用。

```
<slot :product="product"></slot> 
```

这一行简单的代码本质上是将子范围之外的东西提供给父组件，如果它希望使用它的话。

如果我们希望我们的父节点能够利用子节点的这种提供，那么我们可以使用 v-slot 属性，让我们看一下我们重构的父节点的例子。

```
<template>
  <div id="app">
    <product-listing>
      <template v-slot:default="slotProps">
        <span class="product_name">{{ slotProps.product.name }}</span>
        <span class="product_rating">{{ slotProps.product.rating }}</span>
        <span class="product_price">{{ slotProps.product.price }}</span>
      </template>
    </product-listing>
  </div> </template> 
<script>
  import ProductListing from './components/ProductListing'

  export default {
    name: 'app',
    components: {
      ProductListing
    }
  }
</script> 
```

在父组件中，最显著的变化是我们传递给`<product-listing>`组件的内容，我们现在使用一个模板标签，它将包含 slotScope 上的内容和产品

需要注意的一点是，模板标签不会在浏览器中呈现，但内容会，我们现在接受子组件提供给我们的范围。

```
<template v-slot:default="slotProps">
  <span class="product_name">{{ slotProps.product.name }}</span>
  <span class="product_rating">{{ slotProps.product.rating }}</span>
  <span class="product_price">{{ slotProps.product.price }}</span> </template> 
```

这里要注意的一个有趣的点是，提供给父类的实际作用域内容看起来是什么样的，如果我们看一下 slotProps 内部，我们会看到下面的内容:-

```
{  "product":  {  "id":  1,  "name":  "Mario Maker 2",  "rating":  4,  "price":  40000  }  } 
```

知道了这一点，我们可以使用 JavaScript 的析构来允许我们直接使用产品变量名，而不需要到处显示 slotProps，让我们看看最终完成的例子。

```
<template>
  <div class="products">
    <h1>A List of Nintendo Games</h1>
    <div>
      <div v-for="product in products" :key="product.id">
        <slot :product="product"></slot>
      </div>
    </div>
  </div> </template> 
<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: "Mario Maker 2", rating: 4, price: 40000 },
        { id: 2, name: "Breath of the Wild", rating: 5, price: 60000 },
        { id: 3, name: "Tetris 99", rating: 4, price: 8000 }
      ]
    }
  }
}
</script> 
```

```
<template>
  <div id="app">
    <product-listing>
      <template v-slot:default="{ product }">
        <span class="product_name">{{ product.name }}</span>
        <span class="product_rating">{{ product.rating }}</span>
        <span class="product_price">{{ product.price }}</span>
      </template>
    </product-listing>
  </div> </template> 
<script>
import ProductListing from './components/ProductListing'

export default {
  name: 'app',
  components: {
    ProductListing
  }
}
</script> 
```

正如你所看到的，在父类中，我们现在使用析构来移除我们之前使用的难看的 slotProps 命名。

这个例子到此结束，希望你能清楚地看到我们如何使用作用域插槽将产品传递给父产品，然后我们可以使用父产品通过插槽传递。

## 为什么作用域槽是混乱的来源？

回想起来，作用域槽绝对是我在学习时最难掌握的概念，我认为困难来自于这样一个事实，即我们将作用域向上传递给父级，同时在父级中对作用域数据进行评估后向下传递内容。

父母和孩子之间的不断来回只是感觉有点奇怪，当学习这些概念时，它肯定会使头脑弯曲一点。

然而，不要害怕学习这些并让它们在你的大脑中固化，在我看来它们是 Vue.js 最重要的概念之一，并使整个框架更加强大。

## 作用域槽的弃用语法

正如我在本文开头提到的，我们讨论的语法是新的 2.6.0 语法，但是旧的语法经常被使用，事实上，我正在从事一个自由职业者的项目，该项目大量使用了作用域插槽，而目前他们使用的是旧的语法，所以让我们快速地看看它的运行情况。

```
<template>
  <div class="products">
    <h1>A List of Nintendo Games</h1>
    <div>
      <div v-for="product in products" :key="product.id">
        <slot :product="product"></slot>
      </div>
    </div>
  </div> </template> 
<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: "Mario Maker 2", rating: 4, price: 40000 },
        { id: 2, name: "Breath of the Wild", rating: 5, price: 60000 },
        { id: 3, name: "Tetris 99", rating: 4, price: 8000 }
      ]
    }
  }
}
</script> 
```

```
<template>
  <div id="app">
    <product-listing>
      <template slot-scope="{ product }">
        <span class="product_name">{{ product.name }}</span>
        <span class="product_rating">{{ product.rating }}</span>
        <span class="product_price">{{ product.price }}</span>
      </template>
    </product-listing>
  </div> </template> 
<script>
import ProductListing from './components/ProductListing'

export default {
  name: 'app',
  components: {
    ProductListing
  }
}
</script> 
```

如您所见，组件是相同的，但是我们在模板标记上使用了稍微详细一些的 slot-scope 属性。

关于这一变化的原因的更多信息，[看一下 RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md) ，它概述了为什么变化的细节。

## 结论

这篇文章代码量很大，正如我前面提到的，作用域槽很容易成为学习框架最棘手的部分，但是在后面的博客文章中，我将展示一些非常强大的利用作用域槽的模式的例子，你将看到更多例子来说明它们是多么重要。