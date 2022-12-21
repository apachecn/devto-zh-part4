# 反应状态究竟是什么？

> 原文：<https://dev.to/jasterix/what-actually-is-react-state-19e9>

国家是一个奇怪的概念。但是我希望这篇短文能帮助你将这个核心概念概念化。

#### 键外卖

React state 使得当状态数据改变时，React 触发组件的重新呈现，然后相应地更新 DOM。

因此，作为开发人员，您不必担心获取正确的元素来更改 DOM，因为 React 会处理这些。

### 入门状态

#### 设定初始状态

这可以通过如下的构造函数来实现:

```
state = {
    toggleOn: true
  }; 
```

Enter fullscreen mode Exit fullscreen mode

#### 更新状态

可用`setState`更新状态。千万不要用`state =`
直接更新状态

```
this.setState{toggleOn: false} 
```

Enter fullscreen mode Exit fullscreen mode

### 这一切是如何走到一起的

##### ( [codepen](https://codepen.io/jasterix/pen/ZdXvxR)

```
class Toggle extends React.Component {
state = {
  toggleOn: true
};

  handleClick= () => {
    this.setState(prevState => ({
      toggleOn: !prevState.toggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.toggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

#### 状态和道具的区别

*   您的组件使用的任何数据都将在状态或[属性](https://dev.to/jasterix/what-exactly-are-react-props-f91)中被访问
*   状态是在内部维护的(而不是像 prop 一样传入)
*   状态允许我们更新组件中的信息，而不需要其父组件以某种方式发送更新的信息
*   构造函数中的初始状态

如果您仍然感到困惑，下面是一个(非常)简化的测试，用于确定一个组件是否应该有状态。从理解什么没有状态开始

##### 无状态

*   如果一个组件通过它的 props 传递它的数据，那段数据不应该处于状态
*   如果一个组件/数据随着时间保持不变，它就不需要/不是状态

##### 状态

*   如果一个组件保存数据，它可能有状态
*   如果一个组件为它的子组件设置数据，它可能有状态

[这里有一篇很棒的文章](https://www.freecodecamp.org/news/get-pro-with-react-setstate-in-10-minutes-d38251d1c781/)，对 React 状态进行了更深入的研究