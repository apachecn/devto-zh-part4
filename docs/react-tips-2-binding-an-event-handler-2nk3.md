# React 提示:2 -绑定事件处理程序

> 原文：<https://dev.to/pcmichaels/react-tips-2-binding-an-event-handler-2nk3>

示例代码[此处](https://github.com/pcmichaels/react-demos/tree/Tip_2_Binding)。

当处理任何风格的 Javascript 时，你迟早会遇到这个问题。问题是，这个在*意味着什么，取决于你在哪里。这可能是 React 的一个特别普遍的问题；想象下面的代码:*

```
onMouseUp={this.onMouseUp} 
```

在 onMouseUp 中，您可能希望以某种方式影响状态:

```
private onMouseUp(e) {
    this.setState({
        dragging: false
    }); 
```

如果运行此命令，您可能会得到以下错误:

> TypeError: this is undefined

我想你会同意，这是再清楚不过的信息了。

# 有约束力的

我在这里雄辩地提出的问题的答案是有约束力的。本质上，你只需告诉你的本地函数*知道*关于*这个*:

```
onMouseUp={this.onMouseUp.bind(this)} 
```

这确实解决了问题；现在，该方法将正确执行。然而，我们在这里实际做的是[在每次页面呈现](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)时创建一个新函数。为了避免这种情况，您可以保持原始代码不变:

```
onMouseUp={this.onMouseUp} 
```

但是随后在构造函数中绑定该方法:

```
constructor(props) {
    super(props);

this.onMouseUp = this.onMouseUp.bind(this); 
```

顺便说一句，如果您碰巧看到以下错误:

> “this”类型的参数不能赋给“MouseEvent”类型的参数。

你可能错过了。绑定；例如:

```
this.onMouseUp = this.onMouseUp(this); 
```

问我怎么知道的！

# 类别属性

另一个更新的(我觉得更简洁的)方法是下面的语法:

```
onMouseDown = (e) => {
    console.log('onMouseDown');
    this.setState({
        dragging: true
    });
} 
```

这不需要任何绑定。

# 参考

[https://reactjs.org/docs/faq-functions.html](https://reactjs.org/docs/faq-functions.html)

这个的原帖是[这里](https://www.pmichaels.net/2019/07/27/react-tips-2-binding-an-event-handler/)