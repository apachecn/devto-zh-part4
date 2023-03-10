# Vue 开发者的 React 钩子

> 原文：<https://dev.to/michi/react-hooks-for-vue-developers-3n9>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/react-hooks-for-vue-developers)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

如果你很久以前看过 react，并被它的一些冗长所吓跑(我指的是你`ComponentDidMount`、`ComponentWillReceiveProps`、`getDerivedStateFromProps`等)。)，再看一看。钩子把功能组件带到了下一个层次。它带来了你能想象到的所有好处，没有课程，没有 T3，没有样板文件。事实证明我并不孤单，因为这些观点中的一些也在官方文件中提到了[谈论钩子背后的动机。](https://reactjs.org/docs/hooks-intro.html#motivation)

让我们比较一些常见的 vue 东西，并使用 react 钩子实现它们，然后列出每个工具的优缺点。这并不是要说服你放弃 vue 而放弃 react，尤其是看到 vue 正在向同一个方向发展(在最后会有更多的介绍)。但是了解其他框架如何实现共同的任务总是好的，因为类似的东西也可能成为 vue 的未来。

## 组件本身

我们对 vue 单个文件组件的最低要求是下面的设置

```
// Counter.vue

<template>
    <div>0</div>
</template>
<script>
    export default {}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在 react
中也是如此

```
function Counter() {
    return <div>0</div>
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，react 组件不一定要存在于自己的文件中，因为它只是一个函数。

## 工作状态

视图〔t0〕

```
// Counter.vue

<template>
    <button @click="increment">{{ count }}</button>
</template>
<script>
    export default {
        data() {
            return {
                count: 1
            }
        },
        methods: {
            increment() {
                this.count++
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

并做出反应

```
import { useState } from 'react'

function Counter() {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)

    return <button onClick={increment}>{ count }</button>
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，react 的`useState`返回一个元组，第二个参数是 set 函数。在 vue 中，可以直接设置值来更新状态。

有了钩子，每当我们的状态/道具被更新时，`Counter`方法就会被再次执行。只是第一次，它用 1 初始化了变量`count`。这基本上就是关于钩子的全部内容。这个概念是少数几个你必须理解钩子的概念之一。

**向前/向后检视**

(+)预定义结构

(-)你不能只是导入一些东西，然后在模板中使用它。它必须被安排在 vue `data`、`methods`、`computed`、`$store`等各种概念中的一个中。这也使得一些值不必要的反应，并可能导致混乱(为什么这是反应？会改变吗？在哪里？)

**反应利弊**

(+)只是一个函数

(-)实际上它是一个每次状态或道具改变时都会执行的函数。这种思维方式对于那些习惯了 react 的旧的无状态功能组件的人来说可能没有问题，但是对于专门使用 vue 的人来说，需要一种新的思维方式。一开始看起来不自然。

(-)钩子有各种各样的规则，规定你必须在哪里以及如何使用它们。

## 传递道具

```
// Counter.vue

<template>
    <div>
        <h1>{{ title }}</h1>
        <button @click="increment">{{ count }}</button>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                count: 1
            }
        },
        props: {
            title: String
        },
        methods: {
            increment() {
                this.count++
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

并做出反应

```
import { useState } from 'react'

function Counter({ title }) {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)

    return (
        <>
            <h2>{title}</h2>
            <button onClick={increment}>{count}</button>
        </>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

**向前/向后检视**

(+)你可以具体说明你的道具类型(不带 TS)

(-)访问方式与 state (this.xxx)相同，但实际上行为不同(比如赋新值抛出警告)。这使得初学者认为他们可以直接更新道具。

**反应利弊**

(+)通俗易懂->道具只是函数自变量

## 子组件

让我们将按钮提取到一个子组件中。

视图〔t0〕

```
// Button.vue

<template>
    <button @click="$emit('handle-click')">
        {{ value }}
    </button>
</template>
<script>
    export default {
        props: ['value']
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

```
// Counter.vue

<template>
    <div>
        <h1>{{ title }}</h1>
        <Button @handle-click="increment" :value="count" />
    </div>
</template>
<script>
    import Button from './Button'

    export default {
        components: {
            Button,
        },
        data() {
            return {
                count: 1
            }
        },
        props: ['title'],
        methods: {
            increment() {
                this.count++
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，vue 引入了一个“新”概念`events`。

反应对方

```
import { useState } from 'react'

function Button({value, handleClick}) {
    return <button onClick={handleClick}>{value}</button>
}

function Counter({ title }) {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)

    return (
        <>
            <h2>{title}</h2>
            <Button value={count} handleClick={increment}/>
        </>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

**向前/向后检视**

(+)明确分离关注点

(+)事件用 vue devtools 玩得很好

(+)事件带有修饰符，使得代码非常干净。例如`@submit.prevent="submit"` <应用 event.preventDefault()

(-)奇怪的大小写规则

(-)学习(事件)的附加概念。实际上，事件类似于浏览器中的本地事件。为数不多的区别之一是它们不会冒泡。

**反应利弊**

(+)我们不会被迫创建单独的文件

(+)没有事件的概念->只是将函数作为道具传入。要更新道具，您也可以只传入一个函数作为道具

(+)总体更短(至少在这个派生的例子中)

一些赞成/反对意见是矛盾的，这是因为最终这都归结于个人偏好。一个人可能喜欢反应的自由，而另一个人喜欢 vue 的清晰结构。

## 插槽

当您想要将模板传递给子组件时，Vue 引入了另一个概念。让我们使传递给按钮的不仅仅是一个字符串成为可能。

```
// Button.vue

<template>
    <div>
        <button @click="$emit('handle-click')">
            <slot>Default</slot>
        </button>
        <slot name="afterButton"/>
    </div>
</template>
<script>
    export default {}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

```
// Counter.vue

<template>
    <div>
        <h1>{{ title }}</h1>
        <Button @handle-click="increment">
            <strong>{{ count }}</strong>
            <template v-slot:afterButton>
                Some content after the button...
            </template>
        </Button>
    </div>
</template>
<script>
    import Button from './Button'

    export default {
        components: {
            Button,
        },
        data() {
            return {
                count: 1
            }
        },
        props: ['title'],
        methods: {
            increment() {
                this.count++
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

`<strong>{{ count }}</strong>`将进入`<slot></slot>`内部，因为它是默认/未命名的槽。`Some content after the button...`将被放置在`<slot name="afterButton"/>`内。

并且在反应中

```
import { useState } from 'react'

function Button({AfterButton, handleClick, children}) {
    return (
        <>
            <button onClick={handleClick}>
                {children}
            </button>
            <AfterButton />
        </>
    )
}

function Counter({ title }) {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)

    return (
        <>
            <h2>{title}</h2>
            <Button value={count} handleClick={increment} AfterButton={() => 'some content...'}>
                <strong>{ count }</strong>
            </Button>
        </>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

**向前/向后检视**

(-)槽可能会混淆。尤其是当您将数据从子组件发送到插槽时。

(-)将插槽向下传递给多个组件更加令人困惑

(-)要学习的另一个概念

这些都是 vue 使用定制模板语言的结果。它基本上是可行的，但是有了插槽，它就变得复杂了。

> vue 3 中的插槽将得到简化

**反应利弊**

(+)没有新概念——既然组件只是函数，那么就创建这样一个函数，并将其作为道具传入即可

(+)甚至不一定是函数。您可以将 template(jsx)保存在一个变量中并传递它。这正是特殊的`children`道具所发生的事情。

## 计算字段

让我们再次简化例子

```
// Counter.vue

<template>
    <div>
        <h1>{{ capitalizedTitle }}</h1>
        <button @click="increment">{{ count }}</button>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                count: 1
            }
        },
        props: ['title'],
        computed: {
            capitalizedTitle() {
                return title.toUpperCase()
            }
        },
        methods: {
            increment() {
                this.count++
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

反应

```
import { useState, useMemo } from 'react'

function Counter({ title }) {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)
    const capitalizedTitle = title.toUpperCase()

    return (
        <>
            <h2>{capitalizedTitle}</h2>
            <button onClick={increment}>{count}</button>
        </>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

在 vue 中，计算字段有两个用途，而不是一个。它们保持模板干净，同时提供缓存。

在 react 中，我们可以简单地声明一个保存所需值的变量来解决保持模板整洁的问题。(`const capitalizedTitle = title.toUpperCase()`)

为了缓存它，我们可以使用 react 的`useMemo`钩子。

```
const capitalizedTitle = useMemo(() => title.toUpperCase(), [title]) 
```

Enter fullscreen mode Exit fullscreen mode

在第二个参数中，我们必须指定在任何字段的值发生变化时使缓存无效所需的字段。

useMemo 是这样工作的:

> 组件之外的标题改变-> `Counter`函数自属性更新后运行- > `useMemo`意识到标题改变，运行作为第一个参数传入的函数，缓存它的结果并返回它。

**向前/向后检视**

(+)清晰明确的关注点分离

(-)您在函数中定义计算字段，但是像访问 state/props 一样访问它们。如果你仔细想想，这完全有道理，但我多次收到同行关于这一点的问题。

(-)这里发生了一些神奇的事情。vue 怎么知道什么时候让缓存失效？

(-)计算字段有两个用途

**反应利弊**

(+)为了保持模板的整洁，没有什么新的概念需要学习，只是把它保存在一个变量中，并在模板中使用那个变量

(+)您可以控制缓存什么以及如何缓存

## 手表

```
// Counter.vue

<template>
    <button @click="increment">{{ capitalizedTitle }}</button>
</template>
<script>
    export default {
        data() {
            return {
                count: 1
            }
        },
        watch: {
            count() {
                console.log(this.count)
            }
        },
        methods: {
            increment() {
                this.count++
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

反应

```
import { useState, useEffect } from 'react'

function Counter({ title }) {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)

    useEffect(() => {
        console.log(count)
    }, [count])

    return (
        <button onClick={increment}>{count}</button>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

`useEffect`的工作方式与`useMemo`非常相似，只是没有缓存部分。

> `setCount`->-`Counter`功能运行->-`useEffect`意识到计数发生变化，将运行效果。

**向前/向后检视**

(+)干净利落，通俗易懂，一针见血！

**反应利弊**

(+)您可以指定多个字段，而不是一个字段

(-)`useEffect`的目的不如 vue 的`watch`明确。这也是因为`useEffect`不止用于一件事。它能处理各种副作用。

## 挂载

对于 ajax 请求来说，在组件已经挂载的情况下做一些事情是一个不错的选择。

视图〔t0〕

```
// Counter.vue

<template>
    <button @click="increment">{{ capitalizedTitle }}</button>
</template>
<script>
    export default {
        data() {
            return {
                count: 1
            }
        },
        mounted() {
            // this.$http.get...
        },
        methods: {
            increment() {
                this.count++
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

并做出反应

```
import { useState, useEffect } from 'react'

function Counter({ title }) {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)

    useEffect(() => {
        // ajax request...
    }, [])

    return (
        <button onClick={increment}>{count}</button>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以像以前一样使用相同的`useEffect`，但是这次指定一个空数组作为第二个参数。它将执行一次，由于没有像之前那样指定状态(`[count]`)，它将永远不会计算第二次。

**向前/向后检视**

(+)干净轻松。

(-)初始化某个东西和在它完成后清理它必须在两个不同的方法中，这使你不必要地跳转，并迫使你将变量完全保存在其他地方(稍后会详细介绍)

**反应利弊**

(-)很抽象。我更喜欢一个专用的方法。酷的是，我有自由去做这件事。

(-) `useEffect`回调不允许返回承诺(导致竞态条件)

(+)在完全相同的函数中清理:

原来`useEffect`有一个相当有趣和简洁的特性。如果您在`useEffect`中返回一个函数，当组件被卸载/销毁时就会用到它。这起初听起来令人困惑，但是为您节省了一些临时变量。

看这个

```
import { useState, useEffect } from 'react'

function App() {
    const [showsCount, setShowsCount] = useState(true);

    return (
    <div className="App">
        <button onClick={() => setShowsCount(!showsCount)}>toggle</button>
        {showsCount && <Counter />}
    </div>
    );
}

function Counter({ title }) {
    const [count, setCount] = useState(1)
    const increment = () => setCount(count+1)

    useEffect(() => {
        const interval = setInterval(() => {
            increment()
            console.log("interval")
        }, 1000)

        return function cleanup() {
            clearInterval(interval)
        }
    }, [])

    return (
        <button>{count}</button>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

有趣的部分在`useEffect`里面。在同一范围内，我们能够创建和清除一个间隔。使用 vue，我们必须先在别的地方初始化变量，这样我们就可以在`mounted`中填充变量，并在`destroy`中清理变量。

## 其他

视图

(+) `v-model`指令

(+)第一方工具，如 SSR、VueX 和 vue-router，与 devtools 配合非常好

(+)现成的 CSS 范围。超级好用的 SCSS

(+)感觉更像传统的 web 开发，让入职变得更容易

**反应**

(+)越来越多的东西成为第一方，成为 react 核心库的一部分(钩子，代码拆分等。)

(+)许多库可供选择

## 结论

vue 在某些方面限制了你，但同时它也以一种干净一致的方式构建了你的代码。

React 并没有给你太多的限制，但是作为回报，你有更多的责任来维护干净的代码。我认为随着钩子的引入，这变得容易多了。

但是当然，随着所有竞争的进行，vue 不会忽视 react 钩子的好处，并且已经发布了基于功能组件的 rfc。它看起来很有希望，我很兴奋它会带来什么！