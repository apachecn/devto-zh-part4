# React 中什么时候真正使用花括号

> 原文：<https://dev.to/jasterix/when-do-you-actual-need-to-use-curly-braces-in-react-25mh>

要回答这个问题，当你不需要花括号时，解释起来会更容易

#### 不需要花括号的 3 种情况:

1.  绕着一根绳子`<Component propKey = " " />`
2.  围绕道具键`<Component propKey = {this.prop.thing} />`
3.  创建 id 或类名`<div id="hello">`

否则，您将需要使用大括号。好消息是，除了例子#2，使用大括号总是更好。

但是这里有一些常见的例子，说明什么时候**需要**使用花括号。

#### 在下列情况下使用花括号:

*   渲染你的 JSX 元素`render() { return goes here }`
*   初始化状态`state = { name: "jeff" }`
*   参考道具`{ this.props.name }`
*   set state〔t0〕
*   正在做字符串插值`I love ${snack.name}`

#### 花括号作用的简要例子:

[codepen](https://codepen.io/jasterix/pen/ZdXvxR)

```
class Toggle extends React.Component {
state = {
  toggleOn: true
};

  handleClick= () => {
    this.setState({
      toggleOn: !this.state.toggleOn
    });
  }

  render() {
    const buttonStyle = {};
    if (this.state.toggleOn) {
      buttonStyle.backgroundColor = "red"
    } else {
      buttonStyle.backgroundColor = "blue"
    }

    return (
      <button style={buttonStyle} onClick={this.handleClick}>
        {this.state.toggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

按照传统，[这里有一个很好的资源](https://reacttraining.com/blog/jsx-the-confusing-parts/)来了解更多关于 JSX 和它的花括号