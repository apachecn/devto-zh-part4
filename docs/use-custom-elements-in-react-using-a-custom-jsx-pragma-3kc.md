# React 中的自定义元素使用自定义 JSX 杂注

> 原文：<https://dev.to/gugadev/use-custom-elements-in-react-using-a-custom-jsx-pragma-3kc>

您可以在此测试一个示例项目:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [古加德夫](https://github.com/gugadev) / [ react-ce-ubigeo](https://github.com/gugadev/react-ce-ubigeo)

### 使用自定义 JSX 杂注为自定义元素提供更好工具的示例。

<article class="markdown-body entry-content container-lg" itemprop="text">

这个项目是用 [Create React App](https://github.com/facebook/create-react-app) 引导的。

## 如何测试

只需运行应用程序并放下/选择`public/`文件夹中的`ubigeo.txt`文件。

## 背景

根据定义和规范，元素不能接受像对象或数组这样的复杂属性。当我们想要在 React 项目中使用这些类型的属性时，这是一个问题。

例如，以下代码不起作用:

```
const App = function() {
  const data = { a: true }
  return (
    <div className="my-app"&gt
      <my-comp data={data} /&gt
    </div&gt
  )
}
```

Enter fullscreen mode Exit fullscreen mode

因为在运行时，作为属性传递的数据使用`.toString()`转换成字符串。出于这个原因，如果你传递一个对象，你最终会收到一个`[object Object]`(因为`{ a: true }.toString()`)。

在 JSX 使用自定义元素的另一个问题是关于*自定义* …

</article>

[View on GitHub](https://github.com/gugadev/react-ce-ubigeo)

点击此处进行在线演示:

[https://codepen.io/gugadev/embed/bPzrXp?height=600&default-tab=result&embed-version=2](https://codepen.io/gugadev/embed/bPzrXp?height=600&default-tab=result&embed-version=2)

> 嘿，你可以在 JSX 代码中使用 web 组件。

是啊，当然。然而，在某些用例中，您不能使用遵循 React 准则的 web 组件，比如传递复杂的属性，如对象和数组，以及绑定*自定义事件*。那么，我们可以做些什么来解决这些问题呢？让我们看看。

## 将对象/数组传递给自定义元素

有一些选择。最简单的方法是使用`JSON.stringify`将其作为属性传递:

```
const App = () => {
  const data = [
    { x: 50, y: 25 },
    { x: 29, y: 47 }
  ]

  return (
    <h1>My awesome app</h1>
    <x-dataset data={JSON.stringify(data)} />
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个选择是使用 ref 来传递对象/数组作为属性而不是属性:

```
const App = () => {
  const ref = useRef()
  const data = [
    { x: 50, y: 25 },
    { x: 29, y: 47 }
  ]

  useEffect(() => {
    if (ref.current) {
      ref.current.data = data // set the property
    }
  })

  return (
    <h1>My awesome app</h1>
    <x-dataset ref={ref} />
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我更喜欢第二个。你呢？

## 绑定自定义事件

当我们处理自定义元素时，这是一个非常常见的情况。当您需要将侦听器附加到自定义事件时，您需要使用 ref 并自己使用`addEventListener`。

```
const App = () => {
  const ref = useRef()
  const data = [
    { x: 50, y: 25 },
    { x: 29, y: 47 }
  ]

  const customEventHandler = function(e) {
    const [realTarget] = e.composedPath()
    const extra = e.detail
    // do something with them
  }

  useEffect(() => {
    if (ref.current) {
      ref.current.data = data // set the property
      ref.current.addEventListener('custom-event', customEventHandler)
    }
  })

  return (
    <h1>My awesome app</h1>
    <x-dataset ref={ref} />
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？但是，我们能让它变得更简单吗？耶！使用自定义 JSX 杂注。

## 创建自定义 JSX 杂注

当我们创建 pragma 时，这不是一个非常简单的方法，但是，一旦这样，你就不需要像上面的例子那样添加额外的逻辑了。您将最终使用自定义元素作为任何常规的 React 组件！

> 下面的代码是一个 [jsx-native-events](https://github.com/calebdwilliams/jsx-native-events) 的分支，我对它进行了扩展以适应我的需要。

首先，什么是 JSX pragma？

**JSX Pragma**

Pragma 就是将 JSX 语法转换成 JavaScript 的函数。React 中的默认编译指令是`React.createElement`。

为了让你明白这一点，让我们看看下面的句子:

```
<button type="submit">
  Hello
</button> 
```

Enter fullscreen mode Exit fullscreen mode

被转换为:

```
React.createElement(
  'button',
  { type: 'submit' },
  'Hello'
) 
```

Enter fullscreen mode Exit fullscreen mode

这就是为什么我们需要导入`React`事件，如果我们不显式地使用它！

那么，如果我们能控制这一转变过程会怎么样呢？这正是一个让我们。所以，我们来编码吧。