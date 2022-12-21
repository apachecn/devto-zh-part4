# 用 Java 思考:用 Java 代码☕学习 React 和 React 钩子

> 原文：<https://dev.to/getd/thinking-in-java-learn-react-and-react-hooks-with-java-code-1ohf>

作为一名长期的 Java 开发人员，我花了一段时间才理解 React 背后的一些神奇之处。这篇文章是我试图用 Java 概念来解释其中的一些。这并不意味着提供从 Java 到 React 的严格映射。

下面是一个 React `Counter`组件。它显示一个计数，并带有一个按钮来增加它。每点击一次按钮，计数就会加 1，并且屏幕上的值会更新。

```
type Props = { initialCount: number };
type State = { currentCount: number };

class Counter extends React.Component<Props, State> {
  // Sets currentCount to initialCount when component is created
  state: State = { currentCount: this.props.initialCount };

  // Renders a text and a button, which increments count by one when clicked.
  render() {
    return (
      <div>
        {this.state.currentCount}
        <button onClick={() =>
          this.setState({ currentCount: this.state.currentCount + 1 })
        }>
          Increment
        </button>
      </div>
    );
  }
}

// Renders Counter at root
const rootElement = document.getElementById("root");
render(<Counter initialCount={0} />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

相同的 React 组件可以用 Java 编写:

```
// The Props class to pass data into Counter, publicly construct-able.
public class Props {
  public final int initialCount;
  public Props(int initialCount) { this.initialCount = initialCount; }
}

public class Counter {
  // The State class to hold internal data of Counter, private only.
  private static class State {
    final int currentCount;
    State(int count) { this.currentCount = count; }
  }

  private State state;
  private Props props;
  private boolean shouldRender;

  // Constructor. Called once per component lifecycle.
  public Counter(final Props props) {
    this.updateProps(props);
    this.setState(new State(props.initialCount));
  }

  // Called by external whenever props have changed.
  public void updateProps(final Props props) {
    this.props = new Props(props.initialCount);
    this.shouldRender = true;
  }

  // Internal state update method for current count.
  private void setState(final State newState) {
    this.state = newState;
    this.shouldRender = true;
  }

  // Only allows render when shouldRender is true, i.e., props or state changed.
  public boolean shouldRender() {
    return this.shouldRender;
  }

  // Returns a 'virtal DOM' node 'Div' that contains a 'Text' node and a 'Button',
  // which increments count by one when clicked.
  public ReactNode render() {
    this.shouldRender = false;
    return new Div(
      new Text(this.state.currentCount),
      new Button("Increment", new OnClickHandler() {
        @Override
        public void onClick() {
          setState(new State(state.currentCount + 1));
        }
      });
    );
  }
}

// Renders Counter at root
public static void renderAt(HTMLElement root) {
  Counter counter = new Counter(new Props(0));
  root.addChild(counter);
  if (counter.shouldRender()) {
    counter.render();
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

对于有 Java 背景的读者来说，下表将一些核心的 React 概念映射成 Java 概念。

| 反应概念 | Java 概念 |
| --- | --- |
| `component` | `class` |
| `props` | 传入构造函数的`parameters`或`updateProps()`方法，内部不可变 |
| `state` | 一组所有的`private variables`，内部不可变 |
| `setState()` | 用一个新组替换以前的私有变量组 |
| `render()` | 创建应用了值的新视图 |

这里需要注意一些有趣的事情:

# `props`vs`state`

在 React 中，`props`用于外部世界与组件进行通信，类似于 Java 的构造函数和公共方法参数。在上面的例子中，它被用来设置它的初始计数值。

另一方面，`state`由组件内部使用，保存只对组件本身重要的数据。这类似于 Java 中的私有变量。然而，父组件的`state`可以成为子组件的`props`。例如，`Counter`的`currentCount`作为`Text`组件的`props`传递给`Text`组件。

`props`和`state`都应该是内部不变的。在 React 中，我们从不直接改变它们的内部值。相反，向组件传递一个新的`props`(如下例)，并使用`setState()`设置一个新的`state`。注意它们在上面的 Java 代码中是如何内部`final`的。

# 无变化，无渲染

React 仅在`props`或`state`改变时渲染组件。这允许它避免不必要的 DOM 更新。在上面的例子中，组件不会重新渲染，直到点击按钮(一个`state`改变)或者`initialCount`改变(一个`props`改变)。这是用上面的`shouldRender()`方法模拟的。

# 虚拟 DOM 节点

`render()`返回*虚拟*节点。它们是描述某种类型的 UI 应该如何呈现的对象。它们不是最终结果。由 React 引擎决定 UI 如何生成并呈现在屏幕上。这使得 React 可以在不同的平台上工作。例如，React.js 呈现 Html `<button>`，而 React Native 呈现 Android `Button`或 iOS `UIButton`。

# 处理`props`变化

现在，让我们简单谈谈 React 生命周期。React 提供了几个[生命周期方法](https://reactjs.org/docs/react-component.html#the-component-lifecycle)。今天我们来看看`componentDidUpdate()`。

假设如果传入的`props.initialCount`已经改变，我们希望组件重置`state.currentCount`。我们可以如下实现`componentDidUpdate()`:

```
class Counter extends React.Component<Props, State> {
  state: State = { currentCount: this.props.initialCount };

  // After props changed, check if initialCount has changed, then reset currentCount to the new initialCount.
  componentDidUpdate(prevProps: Props) {
    if (prevProps.initialCount !== this.props.initialCount) {
      this.setState({ currentCount: this.props.initialCount });
    }
  }

  render() {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这可以用 Java 写成:

```
class Counter {
  ...
  // Called by external whenever props have changed.
  public void updateProps(final Props props) {
    final Props prevProps = this.props;
    this.props = new Props(props.initialCount);
    this.shouldRender = true;
    this.componentDidUpdate(prevProps);
  }

  private void componentDidUpdate(final Props prevProps) {
    if (prevProps.initialCount != this.props.initialCount) {
      setState(new State(this.props.initialCount));
    }
  }
  ...
}
Counter counter = new Counter(new Props(0));
counter.updateProps(new Props(100)); 
```

Enter fullscreen mode Exit fullscreen mode

外界调用`updateProps()`更新`Counter`的`props`。在这里，`updateProps()`保存了`prevProps`，并将其传入`componentDidUpdate()`。这允许组件检测到一个`props`变化并相应地进行更新。

还要注意，设置 new `props`不需要创建新的组件实例。在上面的例子中，相同的`Counter`组件被新的`props`重用。事实上，React 试图使用一些智能 DOM 匹配和`key`道具尽可能多地重用现有组件。只有在当前 DOM 树上找不到新组件时，它才会创建新组件。

# 反应钩

如果你正在学习 React，你必须学习钩子，因为它是新的标准(一件好事)。让我们快速看一下 React Hooks 中的等价代码:

```
const Counter = ({ initialCount }: Props) => {
  const [currentCount, setCurrentCount] = React.useState(initialCount);

  React.useEffect(() => {
    setCurrentCount(initialCount);
  }, [initialCount]);

  return (
    <div>
      {currentCount}
      <button onClick={() => setCurrentCount(currentCount + 1)}>
        Increment
      </button>
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

代码更加简洁，因为每一行都隐藏了很多东西。

下面这条线用的是`React.useState()`。它一石二鸟(对不起，🥺).鸟

```
 const [currentCount, setCurrentCount] = React.useState(initialCount); 
```

Enter fullscreen mode Exit fullscreen mode

*   它将`state.currentCount`设置为类似于 Java 构造函数的`initialCount`，并且
*   返回一个相当于 Java 中使用的`setState()`方法的`setCurrentCount()`函数。

使用这种模式的好处是可以将一个单独的`state`对象分解成多个简单的值，每个值都由自己的`useState()`方法控制。

接下来，下面的代码行使用`React.useEffect()`创建一个`effect`，它在组件每次更新时运行。

```
 React.useEffect(() => {
    setCurrentCount(initialCount);
  }, [initialCount]); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`effect`被绑定到`initialCount`值(注意`useEffect()`的最后一个参数)。这告诉`useEffect`仅在`initialCount`改变时运行`setCurrentCount(initialCount)`。这相当于下面的 Java 代码:

```
 private void componentDidUpdate(final Props prevProps) {
    if (prevProps.initialCount != this.props.initialCount) {
      setState(new State(this.props.initialCount));
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

除了这篇文章，React 和 Hooks 还有很多其他的魔法。如果你想了解更多关于这个话题的信息，请在下面留下你的评论❤️❤️❤️