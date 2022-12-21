# 组件词汇表📖

> 原文：<https://dev.to/_smellycode/component-glossary-451b>

> 最初发表于[https://smellycode.com/component-glossary/](https://smellycode.com/component-glossary/)

[![](img/75752236760621a8114a34a74b3c013f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sX3S_MbV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://smellycode.com/static/d13221366e77db5c8133d7dfbc2a03b5/4efde/component-glossary-cover-img.jpg)

组件是现代 web 应用程序的基本构件。它们帮助 web 开发人员将复杂的用户界面分解成独立的小块或小块，这些小块或小块可以重用，并可以与其他小块或组件一起使用。通常，组件是

> "较大整体的一部分或元素，尤指机器或车辆的一部分."~ [谷歌](https://www.google.com/search?rlz=1C5CHFA_enIN836IN837&ei=stAJXdmCIsX1vgSa7YmYDA&q=define%3Acomponent&oq=define%3Acomponent&gs_l=psy-ab.3...126435.129603..129948...3.0..0.165.2102.7j12......0....1..gws-wiz.......0i71j0i67j35i39.a9m9W10kFE8)

本文用文字和代码解释了各种类型的组件。

### 功能组件

函数组件是 JavaScript 函数，它接受称为 *props* 的输入，并返回一个 [React 元素](https://reactjs.org/docs/rendering-elements.html)作为输出。下面介绍一个简单的`Greetings`函数组件。

```
function Greetings(props) {
  return <h1>Hello {props.name}</h1>; }

// With arrow function
// const Greetings = props => <h1>Hello {props.name}</h1>; 
```

人们经常把功能组件和“功能组件”混为一谈。**如果功能正常或工作正常，每个部件都是功能部件**。😀

React 不会[实例化](https://javascript.info/constructor-new)功能组件。这意味着不能用 [ref 属性](https://reactjs.org/docs/refs-and-the-dom.html)访问它们。缺乏实例化也使得功能组件无法访问[生命周期挂钩](https://reactjs.org/docs/state-and-lifecycle.html#adding-lifecycle-methods-to-a-class)。

功能组件没有任何状态，除非它们被[钩住](https://reactjs.org/docs/hooks-intro.html)。

### 类组件

用 [ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)创建的组件被称为*类组件*。类组件扩展基类 [React。组件](https://reactjs.org/docs/react-component.html)。与功能组件不同，类组件可以有状态并访问生命周期方法。类组件定义了一个`render`方法，该方法返回一个 react 元素作为输出。这里有一个简单的`Clock`组件来显示时间。

```
class Clock extends React.Component {
  state = { now: new Date() };

  intervalId = null;

  updateTime = () => this.setState({ now: new Date() });

  componentDidMount() {
    this.intervalId = setInterval(() => this.updateTime(), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.intervalId);
  }

  render() {
    return <p>{this.state.now.toLocaleTimeString({}, { hour12: true })}</p>;
  }
} 
```

可以用 *ref 属性*访问类组件的实例。

```
class App extends React.Component {
  clockRef = React.createRef();

  componentDidMount() {
    // instance of the clock component
    console.log(this.clockRef.current);
  }

  render() {
    return <Clock ref={this.clockRef} />;
  }
} 
```

### 纯成分

我们来讨论一个简单的`Greetings` [反应。分量](https://reactjs.org/docs/react-component.html)第一。

```
class Greetings extends React.Component {
  render() {
    console.count('Greetings --> render');
    return <p>Hello {this.props.name}!</p>;
  }
} 
```

它用一个作为道具传递的`name`打招呼。一个额外的 [console.count](https://developer.mozilla.org/en-US/docs/Web/API/Console/count) 语句被添加到`render`方法中来计算执行次数。

下面的`App`组件从表单输入控件中获取`name`,并将其传递给`Greetings`组件。

```
class App extends React.Component {
  state = { name: 'Sheldon', text: '' };

  handleChange = event => {
    this.setState({ text: event.target.value });
  };

  handleSubmit = event => {
    event.preventDefault();
    this.setState({ text: '', name: this.state.text });
  };

  render() {
    return (
      <div>
        <form onSubmit={this.handleSubmit}>
          <input
            type="text"
            value={this.state.text}
            required
            onChange={this.handleChange}
          />
          <input type="submit" value="Greet" />
        </form>
        <Greetings name={this.state.name} />
      </div>
    );
  }
} 
```

当用户与输入控件交互时，它会更新`App`组件的状态。React 调用`App`组件的`render`方法——带有更新的状态和属性——并且*其子组件*到[创建一个新的 React 元素树用于区分](https://reactjs.org/docs/reconciliation.html)。尽管`Greetings`组件的状态和属性没有改变，React 仍然调用`Greetings`组件的`render`方法。

在大型应用程序中，这种**不必要的`render`方法执行会产生性能问题，并使用户界面陷入困境**。使用[*shouldcomponentwupdate*](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)生命周期方法来避免这些不必要的组件重新渲染。默认情况下，`shouldComponentUpdate`返回 true，但是它的实现很容易被覆盖。让我们为`Greetings`组件重写`shouldComponentUpdate`。

```
class Greetings extends React.Component {
  shouldComponentUpdate(nextProps) {
    // Re-render only when the `name` prop changes.
    return this.props.name !== nextProps.name;
  }

  render() {
    console.count('Greetings --> render');
    return <p>Hello {this.props.name}!</p>;
  }
} 
```

在第一次渲染后，`Greetings`组件仅在`name`属性改变时才被重新渲染。

为了解决同样的问题，React 引入了 React 的一个变种。名为[的组件做出反应。隐式实现了`shouldComponentUpdate`的 PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent) 。**隐式实现通过引用比较道具和状态(浅层比较)**。还是写纯版本的`Greetings`吧。

```
class PureGreetings extends React.PureComponent {
  render() {
    console.count('Pure Greetings --> render');
    return <span>Hello {this.props.name}!</span>;
  }
} 
```

这里是有完整代码的[笔](https://codepen.io/smellycode/pen/QXgjzq?editors=0010)。

### 受控/非受控组件

使用表单元素有点乏味。从表单元素中获取数据需要大量的废话。这是因为表单元素在内部维护自己的状态。开发人员必须向 JavaScript 抛出几行代码来完成这项工作。React 中的表单元素也不例外。开发人员处理表单元素的方式决定了该元素是受*控制还是不受*控制的元素/组件。如果表单元素的值由 React 控制，那么它就被称为“受控组件”，否则称为“非受控组件”。

**受控组件不会在用户交互时改变其状态**。只有当父组件决定不接受用户输入时，状态才会改变，比如下面的`SubscriptionForm`组件( [Codepen](https://codepen.io/smellycode/pen/LKbEoX?editors=0010) )。

```
class SubscriptionForm extends React.Component {
  handleSubmit = event => {
    event.preventDefault();
  };
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <input type="email" value="smelly@smellycode.com" />
        <input type="submit" value="Subscribe" />
      </form>
    );
  }
} 
```

*为什么变化不被兑现？*这是因为电子邮件输入的`value`属性被设置为`smelly@smellycode.com`。当 React 在渲染树上运行[差分算法](https://reactjs.org/docs/reconciliation.html)时。它总是以`smelly@smellycode.com`的形式获得电子邮件输入，所以不管用户输入什么，它最终都会呈现相同的值。让我们通过设置一个事件监听器来解决这个问题，这个监听器将更新`change` event( [Codepen](https://codepen.io/smellycode/pen/wLoKRR?editors=0010) )上的状态。

```
class SubscriptionForm extends React.Component {
  state = { email: '' };

  handleSubmit = event => {
    event.preventDefault();
    console.log('Values --> ', this.state);
  };

  handleChange = event => this.setState({ email: event.target.value });

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type="email"
          value={this.state.email}
          onChange={this.handleChange}
        />
        <input type="submit" value="Subscribe" />
      </form>
    );
  }
} 
```

输入表单元素的所有内容都由 React 控制。这就是它被称为“受控组件”的原因。

对于“非受控组件”，React 不处理表单数据。多姆会照顾他们。这里有一个不受控制的版本`SubscriptionForm`。

```
class SubscriptionForm extends React.Component {
  inputRef = React.createRef();

  handleSubmit = event => {
    event.preventDefault();
    console.log('Value -->', this.inputRef.current.value);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <input type="email" ref={this.inputRef} />
        <input type="submit" value="Subscribe" />
      </form>
    );
  }
} 
```

详细的比较请参考文章。

### 高阶组件

假设有一个应用程序，它有一些格式错误的组件- *组件，这些组件的元素/子元素是无效的 react 元素*。呈现这些组件会破坏用户界面。

```
// A sample malformed component.
class MalformedComponent extends React.Component {
  render() {
    // {new Date()} is not a valid react element. Rendering it will throw an error.
    return <p>Now:{new Date()}</p>;
  }
} 
```

我们需要实现一个错误处理机制来避免崩溃。React 提供了[错误边界 API](https://reactjs.org/docs/error-boundaries.html)来处理这样的错误。所以我们将`MalformedComponent`重构为:

```
class MalformedComponent extends React.Component {
  state = {
    error: null
  };

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { error };
  }

  render() {
    if (this.state.error) {
      return (
        <details>
          <summary>Ouch! Things are messed up. We are sorry. 👾</summary>
          <pre style={{ color: `red` }}>{this.state.error.stack}</pre>
        </details>
      );
    }
    return <WrappedComponent {...this.props} />;
  }
} 
```

添加错误边界只会修复`MalformedComponent`。我们还需要修复其他组件，这意味着我们需要为其他组件添加错误边界。

我们如何做到这一点？嗯，一种方法是在每个畸形的组件中添加错误处理代码，就像我们上面做的那样。但是这会使我们的组件维护起来有点麻烦，而且不太干燥。

*如果我们写一个函数来填充错误处理代码会怎么样？嗯，我们可以写，但是我们*不应该写*，因为我们将修改现有的组件，这是不推荐的，可能会导致意想不到的行为。*

如果我们编写一个函数，它接受一个格式错误的组件并返回一个新组件，这个新组件用错误边界包装了格式错误的组件，会怎么样？有意思！唯一的问题是，它将在我们的组件树中添加一个新的包装器组件，但是我们可以忍受它。我们来编码吧。

```
const withErrorBoundaries = WrappedComponent => props => {
  return class extends React.Component {
    state = {
      error: null
    };

    static getDerivedStateFromError(error) {
      // Update state so the next render will show the fallback UI.
      return { error };
    }

    render() {
      if (this.state.error) {
        // Fallback ui.
        return (
          <details>
            <summary>Ouch! Things are messed up. We are sorry. 👾</summary>
            <pre style={{ color: `red` }}>{this.state.error.stack}</pre>
          </details>
        );
      }
      return <WrappedComponent {...this.props} />;
    }
  };
}; 
```

`withErrorBoundaries`可用于任何畸形组件。

```
const SafeComponent = withErrorBoundaries(MalformedComponent); 
```

[https://codepen.io/smellycode/embed/mZMPBL?height=600&default-tab=result&embed-version=2](https://codepen.io/smellycode/embed/mZMPBL?height=600&default-tab=result&embed-version=2)

这正是高阶组件的意义所在。这是一种促进组件逻辑可重用性的模式。你可以把一个特设看作是一个接受一个组件并返回一个新组件的函数。关于 HOCs 的深入解释可以在[这里](https://reactjs.org/docs/higher-order-components.html)找到。

### 哑元件

哑组件也被称为*表示性*或*无状态*组件。它们大多包含 HTML 和样式。哑组件的目的是**使用*道具*** 渲染 DOM。哑组件不会加载或改变任何数据。哑组件所需的数据作为输入/道具与动作一起传递。这就是为什么哑组件没有任何与数据相关的状态。这使得它们更易于重用和管理。这里有一个非常基本的`Greetings`哑组件:

```
function Greetings(props) {
  return <h1>Hello {props.name}</h1>; } 
```

### 智能/容器组件

智能组件也被称为*容器组件*。智能组件知道如何加载和改变数据。有时智能组件仅仅充当容器，将数据传递给子组件作为道具。智能组件也可以有状态和更新状态的逻辑。带有状态和逻辑的简单`Clock`组件。

```
class Clock extends React.Component {
  state = { now: new Date() };

  intervalId = null;

  tick = () => this.setState({ now: new Date() });

  componentDidMount() {
    this.intervalId = setInterval(() => this.tick(), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.intervalId);
  }

  render() {
    return <p>{this.state.now.toLocaleTimeString()}</p>;
  }
} 
```

你可以在 Shade.codes 上阅读更多关于[哑元件和智能元件的内容。](https://www.shade.codes/dumb-components-and-smart-components/)