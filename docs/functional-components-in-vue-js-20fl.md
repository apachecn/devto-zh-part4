# Vue.js (2.x)中的功能组件

> 原文：<https://dev.to/vhoyer/functional-components-in-vue-js-20fl>

我在我的个人博客 :D 上重新发布了这篇文章

* * *

那么，让我们从头开始，什么是功能组件？这些组件更加轻量级，因为它们没有任何数据或计算数据，也没有生命周期事件。它们可以被看作是传递给它的参数改变时重新执行的函数。

要了解更多信息，你可以阅读它的官方文件，或者诺拉·布朗写的很酷的博客文章。他们也有一个修改的 API，原因我还不知道，但现在我提到了它，我很好奇，所以我可能会尝试检查它。

但是真的有那么好吗？老实说，我真的不知道；我只是相信其他人。因为它不需要管理反应，所以应该更好，因为它运行更少的代码来获得相同的结果。但是好多少呢？我不知道。我找不到答案，我希望有人能在评论中回答这个问题。

你猜怎么着我会把这篇文章推送给核心团队(又名[莎拉·德拉斯纳](https://sarah.dev/))，我们都希望能得到我们的答案，好吗？😂 😅

## 这个丑陋的部分

好的，vue 中的功能组件很酷，但也有一些问题，对吗？我的意思是，你可以很好地使用`render()`函数来做所有的事情，并且它会很高兴，因为使用 render 函数你可以更好地组织你的代码。

> 但是当你如此习惯于`<template>`时，学习这个语法的障碍是如此之高，更不用说当你的项目中的其他人都习惯于模板时，你将使用这个渲染函数，现在我们的项目中有两个语法，这都是你的错，你应该为自己感到羞耻，你这个自作聪明的家伙。
> —为了美观，请快速阅读😎

您还可以尝试 React 方式，并在项目中添加在 js 中使用 html 的 JSX 语法，配置 webpack 以理解该语法，BUUUUT

> ...当你如此习惯于`<template>`的时候，学习这种语法的障碍是**与另一个相比不那么**高**，但仍然是**，更不用说当你的项目中的其他人都习惯于模板时，你将使用这个渲染函数**和 JSX** 的事实，现在我们的项目中有两种语法，这都是你的错，你应该为自己感到羞耻，你这个自作聪明的家伙。
> ——为了美观，请快速阅读😎

我知道是因为我试过这么做( *cuz* 我是个自作聪明的人(这个俚语还在用吗？这是我在学校学的😂(现在我感觉我是在用 lisp 编程))但是我的渲染函数语法没有通过代码审查。

所以，我们都希望同意 Vue 简单易用，我们应该坚持使用模板语法，因为它是`s i m p l e r`。现在，如果你有一个自作聪明的团队，并且你们都喜欢在同一个项目中使用模板和渲染功能，那么就去吧，不要听我的，~~还有，把你的 recuiter 的电子邮件~~发给我。

* * *

顺便说一句，我在 Vue.js 的功能组件上遇到了一些问题，我想在这里发泄一下，希望能帮助有同样问题的人:

*   你究竟如何从模板中调用一个`method`？这可能吗？
*   我的道具在哪里？还有我的`$listeners`和`$attrs`？
*   为什么 vue 在功能组件中找不到我的自定义组件，尽管它已经用`components`选项注册了？
*   为什么我从外部放在组件上的自定义类没有被应用？

### 从模板中执行功能

> “我通过 props 接收了一些数据，但是我想使用一个自定义函数很好地格式化它。我该怎么做？我得到一个`undefined is not a function`错误，我要疯了！”
> —想通之前的我

考虑组件的以下`<script>`部分:

```
<script>
export default {
  name: 'DisplayDate',
  props: {
    date: {
      type: String,
      required: true,
    },
  },
  methods: {
    format(date) {
      return new Date(date).toLocaleString()
    },
  },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

出于某种原因，功能组件不能访问 vue 实例，我想这是因为一开始就没有 Vue 实例，但我可能是错的。因此，要访问这些方法，我们不能只:

```
<template functional>
  <span>{{ format(date) }}</span>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我们必须走另一条路，只是`format`不行，我们必须做一个`$options.methods.format(date)`。好了，这个管用。很丑，但是很管用。谁有建议让这个更好？

```
<template functional>
  <span>{{ $options.methods.format(date) }}</span>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，如果你执行这个，你会注意到我说它有效是在骗你...

### 访问道具、监听器、attrs？

它不起作用的原因是因为，同样，没有 Vue 实例，所以当 Vue 加载器将你的模板转换成纯 JavaScript 时，它找不到你刚刚输入的`date`。它需要一个上下文，所以你必须声明一个路径让 Vue 找到它，就像我们对方法所做的那样。

```
<template functional>
  <span>{{ $options.methods.format(props.date) }}</span>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

> “还有`$attrs`和`$listeners`，我真的想做一个完全可扩展的开闭组件，所以我有点需要这个“
> ——我也是

那些也可以买到，只是在不同的地方。`$attrs`现在在`data.attrs`，`$listeners`在`listeners`(这是`data.on`的别名，但作为建议，我会坚持使用新的`listeners`)。

#### `$attrs`

对于那些甚至不知道这是一件事的人，让我澄清一下。在非功能组件中，`$attrs`用来表示每一个传递给你的组件的属性是否在 props 中声明。这意味着，如果我们有像下面这样调用的`DisplayDate`组件:

```
<div>
  <DisplayDate
    :date="'6 Dec 1999'"
    aria-label="6 of December of 1999 was a long time ago, but not so much"
  />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们有声明，就像我们已经定义的那样(`<span>{{ $options.methods.format(props.date) }}</span>`),`aria-label`属性将被忽略。但是如果我们像下面这样声明`DisplayDate`，传递给`DisplayDate`的额外属性将被应用到 span，正如我们指出的。

```
<template functional>
  <span v-bind="data.attrs">{{ $options.methods.format(props.date) }}</span>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

但是我们当然是在功能区。没有什么是容易的，API 是不同的🤷‍♂️.当我们谈论功能组件时，现在`data.attrs`只包含传递给组件的属性，但只包含道具上未声明的属性，在非功能组件中`$attrs`的值为`{ date: '...', ariaLabel: '...' }`，在功能组件中`data.attrs`的值为`{ ariaLabel: '...' }`，而`props`的值为`{ date: '...' }`。

#### `$listeners`

同样的事情发生在`$listeners`上，但是对于事件来说。这意味着，当你试图将`@click`事件应用到一个组件，但是你没有显式地声明它，它将不起作用，除非你使用`$listeners`来代理处理不同元素或组件的监听器。

```
<!-- this is explicitly declaration -->
<button @click="$emit('click')">Click me</button>

<!-- this is the 'proxing' declaration -->
<button v-on="$listeners">Click me</button>

<!-- this is the 'proxing' declaration for functional components -->
<button v-on="listeners">Click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

同样，功能性和非功能性组件 API 之间也有所不同。非功能组件自动处理`.native`事件，而功能组件不确定是否有根元素来应用`.native`事件，所以 Vue 公开了`data.nativeOn`属性来处理您想要的`.native`事件。

<figure>

### 在组件上声明 css 类之外

<figcaption>Passing a class to a custom component</figcaption>

</figure>

```
<MyTitle
  title="Let's go to the mall, today!"
  class="super-bold-text"
/> 
```

Enter fullscreen mode Exit fullscreen mode

你可能面临的另一个问题是关于课程。通常在 Vue 中(从今天开始)，当你将一个类传递给你的定制组件时，不需要显式地配置任何东西，它将被应用到你的组件的根元素，不同于 react，它明确了类的去向。

以上面的例子为例——假设 css 类做了它所说的事情，并且标题没有在 css 中定义`text-weight`,它是一个非功能性组件——标题将显示为**粗体**文本。

现在，如果我们像下面这样编辑`MyTitle`组件，将它转换成一个功能组件，呈现的文本将不再加粗，这可能会让人感到非常沮丧，我知道这一点，因为我就是这样感觉的😅。

<figure>

```
-<template> +<template functional>
   <span>
-    {{ title }} +    {{ props.title }}
   </span>
 </template>

 <script>
 export default
   props: ['title'] // disclaimer: I don't recommend the array syntax for this
 }
 </script> 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>No, it's not nice to have a title with an span tag, this is a mere example, just focus on the vue stuff, titles should more semantic tags like `h1`, `h2` or `strong`.</figcaption>

</figure>

这是因为...那只是因为我们使用了功能组件，它们就是这样...🤷‍♂️.现在，严肃地说，要完成这项工作，你必须增加一点代码，这没什么，真的:

<figure>

```
@@ -0,5 +0,5 @@
 <template functional>
-  <span> +  <span :class="data.staticClass">
     {{ props.title }}
   </span>
 </template> 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>I'd advocate this syntax is better and it should be used in non-functional components too, but thats just a comment.</figcaption>

</figure>

`data.staticClass`表示传递给你的组件的所有类(我假设只有非动态类，稍后会检查，希望我会记得编辑帖子)。所以你能做的就是使用这个变量与你可能声明的其他类合并:

```
<span
  :class="[data.staticClass, {
    'another-class': prop.someProp,
  }"
>
  {{ props.title }}
</span> 
```

Enter fullscreen mode Exit fullscreen mode

### 自定义组件内的功能组件

所以这里我们有一个问题。一个我不知道如何优雅解决的问题。自定义组件不能在功能组件中声明，至少不能以您期望的方式声明。vue 导出上的`components`属性:

```
<template functional>
  <MyCustomComponents1>
    I'd better be sailing
  </MyCustomComponents1>
</template>

<script>
export default {
  components: { // <- this here
    MyCustomComponents1,
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

就是不管用。它将显示空白文本“我最好是在航行”，因为它不能呈现未知的组件。

尽管在下面声明了它，但 Vue 并不关注那个属性，更糟糕的是，它甚至没有说任何东西，比如警告或错误:“警告，组件不能在功能组件上注册”之类的。`components`属性没有用。

现在，有人已经提出了这个问题，并提出了解决这个问题的方法，但是我真的不喜欢它的样子😅，我的意思是，看一看:

```
<template>
  <component :is="injections.components.MyCustomComponents1">
    I'd better be sailing
  </component>
</template>

<script>
import MyCustomComponents1 from '...'

export default {
  inject: {
    components: {
      default: {
        MyCustomComponents1,
      }
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

还可以选择在全局范围内注册您需要的所有组件，或者在承载您的功能组件的父组件上注册您需要的组件。

<figure>

后者不是一个明智的选择，因为它使两个组件——父组件和功能组件——非常紧密地耦合在一起，这通常不是一个好主意。

<figcaption>Registering components in the global scope:</figcaption>

</figure>

```
import Vue from 'vue'
import MyCustomComponents1 from '...'
// And so on...

Vue.component('MyCustomComponents1', MyCustomComponents1)
Vue.component('AndSoOn', AndSoOn)
//...

new Vue({
  el: '#app',
  // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个问题让我想到函数组件并没有被认为是与模板语法一起使用的，因为在函数组件中使用定制组件的唯一合理的方法是使用 render 函数，看，它很优雅:

```
import MyCustomComponents1 from '...'
//...
render(h) {
  return h(MyCustomComponents1, {}, ['I\'d better be sailing'])
} 
```

Enter fullscreen mode Exit fullscreen mode

## 这一切怎么了？

当你在做函数模板时，你必须想象，就像你正在写一个返回 JSX 语法的函数，Vue 加载器或多或少像这样调用你的模板:

```
render(h, { data, listeners, $options, /* the rest of the exposed variables...*/ }) {
  return (
    <template functional>
      <component
        :is="injections.components.MyCustomComponents1"
        v-bind="data.attrs"
        v-on="listeners"
        :class="data.staticClass"
      >
        {{ $options.methods.format(props.date) }}
      </component>
    </template>
  )
}, 
```

Enter fullscreen mode Exit fullscreen mode

所以我们只能访问这些参数，除此之外别无其他。这样做的问题是，当你使用渲染函数语法或 JSX 的功能组件时，你可以访问函数体来进行析构、上下文化、分离、处理数据，如下所示。

```
import MyCustomComponents1 from '...'
import { format } from '...'

render(h, { data, listeners }) {
  const { date } = data.props

  // this is not proper JSX, but I hope you get the point
  return (
    <template functional>
      <MyCustomComponents1
        v-bind="data.attrs"
        v-on="listeners"
        :class="data.staticClass"
      >
        {{ format(date) }}
      </MyCustomComponents1>
    </template>
  )
}, 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很小的例子，但是我希望我能把这个想法表达清楚。组件标记语法又变得简单易读，但是当你在 vue functional component 中使用模板语法时，你不能访问这部分功能。

## 未来？

我真的只是希望[那个有争议的征求意见稿](https://github.com/vuejs/rfcs/pull/42)(编辑:这个已经更新了，现在我们正在讨论[这个](https://github.com/vuejs/rfcs/pull/78))能够公开，我们会得到这个更好的语法，它具有我们都想要的性能和可读性的所有好处。

无论如何，我希望我能帮助你解决你可能面临的任何问题，我很难在那里找到一些信息，我希望这篇文章能让你少一点困难。谢谢你读到这里，我希望你有一个美好的一天，下次见。