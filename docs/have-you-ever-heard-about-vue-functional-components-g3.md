# 你听说过 Vue 功能组件吗？

> 原文：<https://dev.to/sanfra1407/have-you-ever-heard-about-vue-functional-components-g3>

首先:欢迎来到我的博客！这是我的第一篇帖子，我想问候你，并对你的到来表示感谢！

所以，让我们继续本帖真正的主题。

# 目录

1.  [什么是 Vue 组件？](#what-is-a-vue-component)
2.  [功能组件](#functional-component)
3.  如何编写一个功能组件？
4.  [TL；博士](#tldr)

## 什么是 Vue 组件？

基本上，一个组件是一个可重用的 [Vue 实例](https://vuejs.org/v2/guide/instance.html)，它有自己的状态、属性、计算属性、方法和生命周期。

很酷的功能，对吧？是的，他们是...但是试着问问你自己:“*我真的总是需要所有这些功能吗？拥有反应式数据和整个[组件的生命周期](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram)总是必要的吗？*“
所有这些问题的答案都是:**不，你没有**。

有时候你只需要有一个组件，它的主要目标是渲染一些东西。

我知道，现在你在问自己:*“我可以用一个普通的组件来做吗？”*
答案是:**可以，可以**。

但是，现在，让我问你一个问题:"*当你可以使用一个**功能组件*** 的时候，你为什么要使用一个普通组件来做这件事？"

## 功能组件

功能组件基本上是没有生命周期的无实例组件。没别的了。这只是一个简单的函数，其目标是呈现一些数据。

为此你也可以称它为**或者 ***哑元*** 。**

 **由于这类组件没有实例，所以速度更快，**更轻，**一次渲染。如你所见，我把**写成了更轻的**，因为没有整个生命周期和反应系统，最终的包更轻，这意味着:

*   需要解析的 JavaScript 代码更少；
*   要处理的内存更少。

所有这些都给了你的应用程序更好的性能，浏览器会感激你的。

## 如何编写一个功能组件？

我很确定，在这一点上，你有这个问题在你的脑海里打转。我想尽量说清楚，就用一些代码来回答你。

我要做的是编写一个*普通组件*并将其转换成一个*功能组件*。

### 正常分量

```
<template>
  <div class="smart-component">
    <span class="smart-component__prop">{{foo}}</span>
    <span class="smart-component__prop">{{bar}}</span>
  </div>
</template>

<script>
  export default {
    name: "SmartComponent",
    props: {
      foo: {
        type: String,
        required: true
      },
      bar: {
        type: String,
        required: false,
        default: "Default value"
      }
    }
  };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

很酷，不是吗？但是您知道每次实例化`SmartComponent`会发生什么吗？

基本上 Vue [创建它的实例](https://vuejs.oimg/lifecycle.png)，增加反应性并安装组件。所有没用的东西，因为`SmartComponent`不会使用反应性。而且，正如我已经说过的，最后一捆会更重。

**让我们用*功能方式*T3 来重写。**

### 功能组件

```
<template functional>
  <div class="dumb-component">
    <span class="dumb-component__prop">{{props.foo}}</span>
    <span class="dumb-component__prop">{{props.bar}}</span>
  </div>
</template>

<script>
  export default {
    name: "DumbComponent",
    props: {
      foo: {
        type: String,
        required: true
      },
      bar: {
        type: String,
        required: false,
        default: "Default value"
      }
    }
  };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。没什么特别的。如你所见，我只改变了几件事:

*   我已经将关键字`functional`添加到模板块中；
*   我已经在`foo`和`bar`道具前添加了`props`关键字。

#### 功能关键字

嗯，你很容易猜到，`functional`关键字是用来对 Vue 说:“*嘿，我要写一个功能组件了！不要增加整个生命周期和反应性，使其过载；我不需要他们！*

#### 道具关键字

由于功能组件没有实例，`this`关键字将是`undefined`。所以，要阅读道具，你需要在每个道具前加上`props`关键字。

[![Woah](img/742d54688f29398bc3e7dfb016dc5b6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--430EvxUB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://giphygifs.s3.amazonaws.com/media/hAK7E1hcIXPgI/giphy.gif)

我知道，我知道...你在问自己:“ *WTF？在`SmartComponent`之前我没有看到任何`this`道具！*
原因是 Vue 自动将`props`、`methods`、`computed`、`$options`对象绑定到组件实例。
所以，基本上，在这个场景中`{{foo}}`和`{{bar}}`是`{{this.foo}}`和`{{this.bar}}`的简称。
**记住:这只在`<template>`块内有效；在`<script>`块中，你必须把`this`关键字放在 props、methods、computed 和$options 之前，否则你会得到一个错误。**

注意:还有另一种方法来编写功能组件，那就是使用*渲染函数*，但是我会写一篇关于它们的更详细的帖子。

## TL；速度三角形定位法(dead reckoning)

当你不需要一个被动的状态和整个组件生命周期时，为了更好的性能，你应该使用功能组件。

* * *

### 跟我上

*   👾 [GitHub](https://github.com/Sanfra1407)
*   📷 [Instagram](https://www.instagram.com/Sanfra1407/)

*如果你喜欢这个帖子，你可以在 [PayPal](https://www.paypal.me/giuseppesanfrancesco) 上给我一个* ☕️ *。*🙂

* * ***