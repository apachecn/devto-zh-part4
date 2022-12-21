# 单向数据流

> 原文：<https://dev.to/fosteman/unidirectional-data-flow-4hb3>

# 单向数据流

React 组件通过 props 进行通信，并且具有父子关系。父组件可以安全地将不可变数据传递给子组件。而孩子可以通过处理程序(回调)将数据推送给父母。一个完美的[通顺](https://facebook.github.io/flux/docs/overview.html)
通顺[官方文档](https://github.com/facebook/flux/tree/master/examples/flux-concepts)

### 背景

> 如果一个组件包含另一个组件，它就是父组件。另一个组件中的一个组件被称为子组件。

```
<Parent>
    <Child />
    <Child />
</Parent> 
```

存在于同一层的组件，比如上面的两个`<Child />`,不共享任何类型的直接关系，即使它们可能就在彼此旁边。他们只“关心”父级`<Parent />`和他们自己嵌套的子级。

### 单向数据流是如何建立的:

*   Props(向下移动)将数据传入`children`的最简单方式是通过`props`

```
render() {
...
  return <Link to={'https://github.com/fosteman/'}>MyGitHub</Link>;
} 
```

`<Link>`通过属性`to`获取他的位置描述符(url)

*   Handlers(向上运动)函数对象可以作为参数传递给其他函数，grace to >*JS 中的一切*都是一个对象。

```
const IncrementField = (props) => (
  <div>
    {props.currentCount}
    <input onChange={props.onChange} />
  </div>
);

class Parent extends Component {
  constructor(props) {
    super(props);
    this.state = { counter: 0 };
    // because components created with classes don’t auto bind component methods, you need to bind them to `this` in the constructor.
    this.handleIncrement = this.handleIncrement.bind(this)
  }
  handleIncrement(amount = 1) {
    // note that immutable state isn't directly modified, instead .setState is used
    this.setState({ counter: this.state.counter + amount });
  }
  render() {
    return <IncrementField currentCount={this.state.counter} onChange={this.handleIncrement} />
  }
} 
```

在`Parent`组件上定义的方法`handleIncrement`通过属性传递给`onClick={this.handleIncrement}`子组件`<IncrementField />`

这样，子组件可以在完全不知道父组件将如何处理数据的情况下向上推送数据。

### Redux

是 React `state`的逻辑延续。如果一个人需要在应用程序中集中他的数据，Flux 就派上用场了。

它是管理应用程序中数据流的一种模式。最重要的概念是数据单向流动。

[看我的文章](https://dev.to/fosteman/redux)关于 Redux！