# Vue 开发者必须知道的 8 个秘密

> 原文：<https://dev.to/the_one/8-secrets-vue-developers-must-know-5la>

*   [内联语句处理程序:处理多个参数](#inline-statement-handler-handle-multiple-arguments)
*   [从功能组件发出事件](#emit-event-from-functional-components)
*   [将所有道具传递给子组件](#pass-all-props-to-the-child-component)
*   [观察父属性的子属性变化](#watch-child-properties-changes-from-parent)
*   [监听路由器视图中的子事件](#listening-for-child-event-in-the-router-view)
*   [Vue 组件生命周期挂钩没有按照您认为的顺序运行](#vue-components-lifecycle-hooks-dont-run-in-the-order-you-think-they-run)
*   [如何知道子组件是否已安装](#how-to-know-if-a-child-component-is-mounted-)
*   [如何知道一个动态注册的 vuex 模块是否被注册](#how-to-know-if-a-dynamically-vuex-registered-module-is-registered-)

* * *

## **行内语句处理程序:处理多个参数**

#### **简短解释:**

在内联语句处理程序中，除了`$event`特殊变量之外，我们还可以访问另一个特殊变量: **`arguments`** 。`$event`只能访问第一个参数，而`arguments`可以访问所有参数。

#### **详细解释:**

在内联语句处理程序中，我们已经知道我们可以访问特殊的`$event`变量。

因此，如果一个子组件发出一个带有参数的事件，我们可以通过使用`$event`来访问它:

**母组件**

```
<template>
  // ...
   <Child @event="someVariable = $event" />
  // ...
</template> 
```

Enter fullscreen mode Exit fullscreen mode

**子组件**

```
export default {
  //...
    emitEvent () {
      this.$emit('event', 'a simple text')
    }
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

当子组件是我们可以访问的组件时，这种方法非常有效，因为我们可以确保只传递一个参数。

但是如果我们使用一个第三方组件/库(比如 dropzone.js)通过 event 传递许多参数呢？？

**`$event`将只能访问第一个参数**。请看这个 codesandbox 例子，它说明了`$event`将只捕捉第一个参数。(*点击按钮，看到控制台*)

[https://codesandbox.io/embed/vue-event-special-variable-mj92o](https://codesandbox.io/embed/vue-event-special-variable-mj92o)

**在这种情况下，我们可以使用`arguments`来代替`$event`，这样所有的参数都是可访问的。**

所以在上面的 codesandbox 中，为了让它工作，我们必须修改`Child.vue`中的第 4 行

*来自:*

```
<GrandChild @event="$emit('event', $event)"/> 
```

Enter fullscreen mode Exit fullscreen mode

*到:*

```
 <GrandChild @event="$emit('event', arguments)"/> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **从功能组件发出事件**

#### **简短解释:**

使用功能组件，意味着我们无法访问`this`上下文。因此，我们不能做`this.$emit()`。但是...我们可以访问`listeners`，所以我们可以做`<button @click="listeners.clicked"></button>`

#### **详细解释:**

功能组件是无状态的(没有反应数据)和无实例的(没有这个上下文)。但是功能组件可以访问一些属性，如`props`、`children`等，最重要的是(在这种情况下)，`listeners`。

根据[Vue docs](https://vuejs.org/v2/guide/render-function.html#Functional-Components):
**listeners:一个包含父注册事件监听器的对象。这是 data.on** 的别名

这意味着我们可以从功能组件中发出事件。威尔:P

**简单的例子:**

```
<template functional>
  <button @click="listeners['custom-event']('message from child')">
    Button from child
  </button>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

**工作示例** ( *点击按钮，打开控制台*)

[https://codesandbox.io/embed/pjvwo81py7](https://codesandbox.io/embed/pjvwo81py7)

如果我们想用渲染函数从功能组件中发出一个事件，该怎么办？我们能做到吗？当然啦！

**简单的例子:**

```
export default {
  functional: true,
  render(createElement, { listeners }) {
    return createElement(
      "button",
      {
        on: {
          click: event => {
            const emit_event = listeners.event_from_child;
            emit_event("Hello World!Is this the message we excpected? :/");
          }
        }
      },
      "Pass event to parent"
    );
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

**工作示例**

[https://codesandbox.io/embed/k2nm1zxv8o](https://codesandbox.io/embed/k2nm1zxv8o)

有人可能想知道我们是否可以用这种方法使用 [`.sync`修饰语](https://vuejs.org/v2/guide/components-custom-events.html#sync-Modifier)。
答案？当然啦！

```
<button @click="listeners['update:message']('some text')">Click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **将所有道具传递给子组件**

假设我们有一个接收`props`的组件，我们想将所有这些道具传递给一个子组件。为了实现这一点，我们可以做:

```
 <ChildComponent v-bind="$props" /> 
```

Enter fullscreen mode Exit fullscreen mode

利用`v-bind`，我们也可以有一个类似于
的对象

```
data: () =>({
  obj: {
    firstName: 'John',
    lastName: 'Doe',
    age: 30
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

并将`firstName`、`lastName`、`age`作为`props`传递给一个子组件，比如:

```
<Child v-bind="obj" /> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **从父**观察子属性的变化

你可能想知道为什么要这样做，你是对的！那是一种不好的做法。但是，有时您使用第三方组件，并希望观察它们的属性以满足您的需求。

*很久以前，在一个项目中，我们使用了一个日期选择器，我们希望能够检测到弹出窗口何时可见。不幸的是，我们别无选择。我发现日期选择器使用了一个`popupVisible`反应属性，但是它没有被库公开。所以我必须从我的组件中观察这个属性。*

下面的代码是**不能**检测变化:

```
 watch: {
    '$refs.datePicker.popupVisible': {
      handler (new_value) {
        console.log('value changed')
      },
      deep: true
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了检测子组件属性的变化，你应该做:

```
 mounted() {
    this.$watch(
      "$refs.picker.popupVisible",
      (new_value, old_value) => {
         //execute your code here
      }
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

**工作示例:**

[https://codesandbox.io/embed/yk1l5zp9l9](https://codesandbox.io/embed/yk1l5zp9l9)

了解更多关于 [`vm.$watch`](https://vuejs.org/v2/api/#vm-watch)

* * *

## **在路由器视图中监听子事件**

你们中的大多数人应该已经知道了，因为这个问题很简单，但是我已经被问了很多次了。

假设您有一个包含嵌套路由的组件:

```
<template>
  //...
  <router-view></router-view>
  //...
</template> 
```

Enter fullscreen mode Exit fullscreen mode

您有一个嵌套路由，如下所示:

```
<template>
  //...
  <button @click="$emit('event')"></button>
  //...
</template> 
```

Enter fullscreen mode Exit fullscreen mode

因此，对应于嵌套路由的组件会发出一个事件。问题是:**如何听那个事件？**

用代码演示的简单答案:

```
<template>
  //...
  <router-view @event="callAMethod"></router-view>
  //...
</template> 
```

Enter fullscreen mode Exit fullscreen mode

*正是！我们收听`router-view`组件*中的事件

* * *

## **Vue 组件生命周期挂钩没有按照你认为的顺序运行**

假设你有两页纸。在家和附近。

当从 Home 切换到 About 时，**About 组件的`created`钩子将在 Home 组件的`beforeDestroy`钩子之前运行。** ( *这里稍待片刻*)

奇怪吗？？通过查看控制台，尝试在下面的工作示例中切换路由。

[https://codesandbox.io/embed/vue-lifecycle-hooks-not-running-in-the-way-you-expect-e4y31](https://codesandbox.io/embed/vue-lifecycle-hooks-not-running-in-the-way-you-expect-e4y31)

作为解决方案(idk，如果这是最好的解决方案)你可以使用[过渡模式](https://vuejs.org/v2/guide/transitions.html#Transition-Modes) **出入**

```
 <transition mode="out-in">
      <router-view></router-view>
    </transition> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **如何知道子组件是否挂载？**

这是我看过的最喜欢的技巧之一[(Vue Dose)](https://vuedose.tips/tips/listen-to-lifecycle-hooks-on-third-party-vue-js-components)

假设你有一个子组件，当子组件的钩子被执行时，你想做一些事情。你可以做:

```
<Child @hook:created="doSomething" /> 
```

Enter fullscreen mode Exit fullscreen mode

因此，*如何知道子组件何时挂载*的问题是:

```
<Child @hook:mounted="componentMountedDoSomething" /> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **如何知道一个动态注册的 vuex 模块是否注册了？**

借助动态[注册/注销](https://vuex.vuejs.org/guide/modules.html#dynamic-module-registration) vuex 模块的能力，我们可以大幅提高性能。

*推荐大家看一篇很有用的文章:[性能优化:懒惰加载 vuex 模块](https://itnext.io/vue-js-app-performance-optimization-part-3-lazy-loading-vuex-modules-ed67cf555976)T3】*

我们可以注册一个 vuex 模块:

`this.$store.registerModule('my-module', MyModule)`

并注销它:

`this.$store.unregisterModule('my-module')`

要知道模块是否已经注册:

```
if (Object.keys(this.$store._modules.root._children).includes('my-module')) {
        // module is registered
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我的时间非常有限，但我很高兴我找到了一些时间来写这篇文章(虽然它花了我一些时间)。我希望它有所帮助，你喜欢阅读它。

如果你留下评论提供你的解决方案，我会很高兴。例如，关于最后一个“秘密”,您可以使用 vuex store 来指示模块是否已注册。

> 这是我第一篇关于开发社区的文章。感谢您的阅读。