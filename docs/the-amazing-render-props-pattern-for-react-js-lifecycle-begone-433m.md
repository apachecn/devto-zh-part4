# React.js 惊人的渲染道具模式——生命周期消失！

> 原文：<https://dev.to/itnext/the-amazing-render-props-pattern-for-react-js-lifecycle-begone-433m>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 渲染道具图案是什么？Render props 模式是我们创建向子组件提供某种数据的组件的一种方式。

我们为什么要那样做？假设我们想做以下事情:

*   **获取数据**，如果有一个组件能够抽象出 HTTP 中的所有乱七八糟的东西，并在完成后为您提供数据，这不是很好吗？
*   **A/B 测试**，当你将一个应用程序投入生产时，你最终会想要改进，但你可能不知道最佳的前进方式，或者你可能想要经常发布并把代码推向生产，但一些功能尚未准备好公开，所以你希望能够有条件地决定某些东西是否可见。

[![alt text](img/c4211fbaf33a6d1760f7da229f014ed4.png "Render props, the pattern that is on fire")](https://res.cloudinary.com/practicaldev/image/fetch/s--ot5e7zwC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7suasq4cy902pekbtzcx.jpg)

如果你有上面的任何一个场景，你就有了可重用的功能。对于可重用的功能，您很可能希望将其抽象为一个函数或一个组件，我们将选择后者。

如果我们可以为这种功能创建组件，并将其提供给某个组件，这不是很好吗？该子组件将不会意识到它正被提供数据。

从某种意义上说，这类似于我们对提供者所做的事情，也类似于容器组件包装表示组件的方式。这听起来有点模糊，所以让我们展示一些标记

```
const ProductDetail = ({ product }) => ( 
  <React.Fragment> 
    <h2>{product.title}</h2> 
    <div>{product.description}</div> 
  </React.Fragment> ) 

<Fetch url="some url where my data is" 
  render={(data) => <ProductDetail product={data.product} /> }
/> 
```

正如我们在上面看到的，我们有两个不同的组件`ProductDetail`和`Fetch`。`ProductDetail`看起来只是一个演示组件。`Fetch`另一方面看起来有点不同。它上面有一个属性 url，看起来它有一个渲染属性来渲染我们的`ProductDetail`。

## 渲染道具说明

我们可以对此进行逆向工程，并弄清楚这是如何工作的。

我们再来看看代码:

```
<Fetch url="some url where my data is" 
  render={(data) => <ProductDetail product={data.product} /> }
/> 
```

我们的 Fetch 组件有一个属性`render`,它似乎采用了一个最终产生 JSX 的函数。事情是这样的，整个渲染道具模式是关于我们在返回方法中调用一个函数。让我通过展示一些代码来解释一下:

```
class Fetch extends React.Component { 
  render() { 
    return this.props.render(); 
  } 
} 
```

这就是最简单的模式。我们使用`Fetch`组件的方式意味着我们至少需要发送一些东西到`this.props.render()`调用中。我们只提取上面的函数调用位来看看:

```
(data) => <ProductDetail product={data.product} /> 
```

从上面我们可以看到，我们需要一个参数数据，而数据似乎是一个对象。好吧，那么数据是从哪里来的？这就是我们的`Fetch`组件，它为我们做了一些繁重的工作，即执行 HTTP 调用。

## 为 HTTP 创建组件

让我们给`Fetch`添加一些生命周期方法，这样看起来就像这样:

```
class Fetch extends React.Component { 
  state = { 
    data: void 0, 
    error: void 0 
  } 
  componentDidMount() { 
    this.fetchData(); 
  } 
  async fetchData() { 
    try { 
      const response = await fetch(this.props.url); 
      const json = await response.json(); 
      this.setState({ data: json }); 
    catch (err) { 
      this.setState({ error: err }) 
    } 
  } 
  render() { 
    if (!this.state.data) return null; 
    else return this.props.render(this.state.data); 
  } 
} 
```

> 快速注意，如果你对`void 0`不熟悉，那只是设置一些东西到`undefined`

好了，现在我们已经稍微充实了我们的组件。我们已经添加了进行 HTTP 调用的方法`fetchData()`，给定了`this.props.url`，我们可以看到，如果没有设置`this.state.data`，我们的`render()`方法将呈现 null，但是如果 HTTP 调用完成，我们将使用 JSON 响应调用`this.props.render(data)`。

然而，它缺少三样东西:

*   **处理错误**，我们应该添加逻辑来处理错误
*   **处理加载**，如果 fetch()调用没有完成，现在我们什么都不渲染，这不是很好
*   **处理 this.props.url** ，这个属性可能最初没有被设置，它可能会随着时间而改变，所以我们应该处理它

### 处理错误

我们可以通过稍微改变我们的`render()`方法来轻松处理这个问题，以迎合是否设置了`this.state.error`，毕竟我们已经在`fetchData()`方法的 catch 子句中编写了设置`this.state.error`的逻辑。

来了:

```
class Fetch extends React.Component { 
  state = { 
    data: void 0, 
    error: void 0 
  } 
  componentDidMount() { 
    this.fetchData(); 
  } 
  async fetchData() { 
    try { 
      const response = await fetch(this.props.url); 
      const json = await response.json(); 
      this.setState({ data: json }); 
    catch (err) { 
      this.setState({ error: err }) 
    } 
  } 
  render() { 
    const { error, data, loading } = this.state; 
    if(error) return this.props.error(error); 
    if (data) return this.props.render(data); 
    else return null; 
  } 
} 
```

上面我们通过调用`this.props.error()`添加了对 this.state.error 的处理，所以这是我们在尝试使用`Fetch`组件时需要考虑的事情。

### 搬运装载

对于这一个，我们只需要添加一个新的状态加载并更新`render()`方法来查看所说的属性，就像这样:

```
class Fetch extends React.Component { 
  state = { 
    data: void 0, 
    error: void 0,
    loading: false 
  } 
  componentDidMount() { 
    this.fetchData(); 
  } 
  async fetchData() { 
    try { 
      this.setState({ loading: true }); 
      const response = await fetch(this.props.url); 
      const json = await response.json(); 
      this.setState({ data: json }); 
      this.setState({ loading: false }); 
    catch (err) { 
      this.setState({ error: err }) 
    } 
  }

  render() { 
    const { error, data, loading } = this.state; 
    if(loading) return <div>Loading...</div>     if(error) return this.props.error(error); 
    if (data) return this.props.render(data);
    else return null; 
  } 
} 
```

现在，我们在处理加载时有点草率，是的，我们为它添加了一个`if`,但是我们渲染的东西很可能使用一个看起来像旋转器或幽灵图像的漂亮组件来改进，所以这值得思考。

## 处理对 this.props.url 的更改

这个 URL 完全有可能改变，我们需要迎合它，除非我们打算像这样使用组件

在这种情况下，您应该跳过这一节，转而看下一节；)

React API 最近发生了变化，在变化之前，我们需要添加生命周期方法`componentWillReceiveProps()`来查看道具是否发生了变化，但是这被认为是不安全的，所以我们必须使用

```
componentDidUpdate(prevProps) { 
  if (this.props.url && this.props.url !== prevProps.url){
    this.fetchData(this.props.url); 
  } 
} 
```

就是这样，这就是我们需要的，让我们展示这个组件的完整代码:

```
class Fetch extends React.Component { 
  state = { 
    data: void 0, 
    error: void 0,
    loading: false 
  } 
  componentDidMount() { 
    this.fetchData(); 
  } 
  componentDidUpdate(prevProps) { 
    if (this.props.url && this.props.url !== prevProps.url) {     
      this.fetchData(this.props.url); 
    } 
  } 
  async fetchData() { 
    try { 
      this.setState({ loading: true }); 
      const response = await fetch(this.props.url); 
      const json = await response.json(); 
      this.setState({ data: json }); 
      this.setState({ loading: false }); 
    catch (err) { 
      this.setState({ error: err }) 
    } 
  } 
  render() {
    const { error, data, loading } = this.state; 
    if(loading) return <div>Loading...</div>
    if(error) return this.props.error(error);
    if(data) return this.props.render(data); 
    else return null; 
  } 
} 
```

要使用我们的组件，我们现在可以输入:

```
<Fetch 
  url={url-to-product} 
  render={(data) => <ProductDetail product={data.product} />} 
  error={(error) => <div>{error.message}</div>} 
/> 
```

## A/B 测试

让我们继续下一个案子。我们迟早可能会有两个主要原因想要使用这个组件有条件地显示代码:

*   **还没有准备好**，我们希望经常部署，我们可能希望只向我们的产品负责人展示新功能，这样我们就可以收集反馈，所以如果我们能够用一个标志来控制这些组件内容的显示，那就太好了
*   **A/B 测试**，假设我们不知道我们要去哪个新的结账页面，在我们的电子商务应用程序中，如果我们能让一半的用户去版本 1，另一半去版本 2，那就太好了。在这种情况下，您可能有两个不同的页面，但如果差异很小，如几个部分的切换，那么这可能是一个很好的候选。

好了，让我们看看如何使用这个组件:

```
<FeatureFlag 
  flag={showAlternateSection} 
  render={()=> <div>Alternate design</div>} 
  else={()=> <div>Normal design</div>} 
/> 
```

上面我们有一个组件`FeatureFlag`和以下属性，让我们分解一下我们打算如何使用它们:

*   **flag** ，这将是特征标志的名称，很可能是一个字符串
*   **render** ，这将是我们调用的一个方法，假设特性标志被启用
*   **else** ，如果特性标志被禁用或不存在，这将是我们调用的方法

## 建筑我们的构件

好了，我们知道如何使用我们的组件，让我们试着构建它:

```
class FeatureFlag extends React.Component { 
  state = { 
    enabled: void 0 
  } 

  componentDidMount() { 
    const enabled = localStorage.getItem(this.props.flag) === 'true'; 
    this.setState({ enabled }); 
  } 
  render() { 
    if(enabled) return this.props.render(); 
    else if(enabled === false) return this.props.else(); 
    else return null; 
  } 
} 
```

好，那么在这里介绍三种状态:

*   **真**，当我们知道标志为真时
*   **假**，当我们知道标志是假的
*   **void 0/undefined** ，标志值尚未解析时

为什么我们需要三种状态？嗯，我们希望确保它准确地呈现它应该呈现的内容，并且不显示它不应该显示的内容，哪怕只有一毫秒。

好吧，这听起来有点疯狂，`localStorage.getItem()`通常反应很快。

是的，当然，我可能有点疯狂，但是如果标志值不在`localStorage`中，而是驻留在我们需要调用的服务上，那么可能需要一些时间来取回值...

所以想象我们的`componentDidMount()`看起来像这样:

```
async componentDidMount() { 
  const enabled = await flagService.get(this.props.flag);
  this.setState({ enabled }); 
} 
```

如果你想把你的标志放在一个服务中，而不是放在`localStorage`中，这是一个很小的改变:

## 总结

渲染道具模式就是渲染一个函数，这个函数本身可以渲染 JSX，正如你所看到的，你可以基于这个设计模式创建非常强大和有用的组件。

希望这篇文章对你有用，如果有用，请给我一些掌声。

哦，如果你在评论中给我留下一些你使用渲染道具模式构建的组件的链接，我会很高兴的。

保持安全，记住永远不是你的错，是编译器的问题；)

### 进一步阅读

*   [渲染道具](https://reactjs.org/docs/render-props.html)图案
*   我在 React 上的免费书籍
*   [我的推特](https://twitter.com/chris_noring)

让我们在该表扬的地方给予表扬。如果没有 https://twitter.com/l0uy，我不会写这篇文章，所以给他一个关注吧:)