# react-原生基础

> 原文：<https://dev.to/thefinnomenon/react-native-basics-28lf>

# 什么是反应？

React 是一个用于构建用户界面的 JavaScript 库。这是一个由脸书创建的开源库，背后有一个庞大的社区。使用 React 的三个主要优点是

*   宣言的
*   基于组件的
*   一次学习，随处写作

## 陈述性的

声明式编程是一种范式，它专注于描述你想要什么，而不是 T2 如何做。另一端是命令式编程，即 **how** 与 **what** 相对。在下面的代码示例中可以很好地观察到这种差异。

```
// Imperative
$("#btn").click(function() {
  $(this).toggleClass("highlight")
  $(this).text() === 'Add Highlight'
    ? $(this).text('Remove Highlight')
    : $(this).text('Add Highlight')
})

// Declarative
<Btn
  onToggleHighlight={this.handleToggleHighlight}
  highlight={this.state.highlight}>
    {this.state.buttonText}
</Btn> 
```

## 基于组件

使用 React，您可以专注于构建管理自身状态的封装组件，然后组合并重用它们来制作复杂的 ui。

```
// Name Component
function Name(props) {
  return <h1>Hello, {props.name}</h1>
}

// App Component w/ 3 Name Components
function App() {
  return (
    <div>
      <Name name="Alice" />
      <Name name="Bob" />
      <Name name="Chris" />
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById("root")) 
```

## 学一次，到处写

React 可以在多种平台上运行，包括能够在服务器、浏览器、移动设备和桌面上进行渲染。

# 什么是 React-Native？

React-Native 是一个将 React 引入移动设备的库。它允许你使用 React 为 Android 和 iOS 创建本地应用。React 原语呈现到本机平台 UI，不像混合解决方案那样基本上将代码包装在 webview 中。

由于支持 iOS 和 Android 应用程序之间的高水平代码重用，这已经成为移动开发的流行选择。此外，由于可以立即看到变化，开发体验真的很愉快，这意味着您不再需要等待您的原生版本编译！

# JSX

JSX = JavaScript XML

这是您将用来编写 React UI 的 HTML 风格的语言。

```
const element = <Text>My name is</Text> 
```

上面的 JSX 最终被呈现为一个原生文本标签。

## 嵌入表达式

JSX 的一个强大功能是能够将任何 JavaScript 表达式嵌入到 JSX 中。要嵌入一个表达式，你只需要用花括号把它括起来。

```
const name = "Chris"

function getAge() {
  return 28
}

const el1 = <Text>My name is {name}</Text>
const el2 = <Text>I'm {getAge()} years old</Text>
const el3 = <Text>2+2={2 + 2}</Text> 
```

## 编译

编译后，JSX 表达式成为常规的 JavaScript 函数调用，并对 JavaScript 对象求值。这允许您在 if 和 for 循环中使用 JSX，将它赋给变量，将其作为参数传递，并从函数中返回。

```
function greet(name) {
  if (user) {
    return <Text>Hello, {name}!</Text>
  }

  return <Text>Hello, anonymous!</Text>
} 
```

## 空标签

如果 JSX 标签没有任何子标签(标签打开和关闭之间的任何东西)，那么它可以使用`/>`自动关闭。

```
const element = <Image source={myPicSrc} /> 
```

# 元素

元素是 React 应用程序的最小构建块。一个元素描述了你想在屏幕上看到什么。

```
const element = <Text>Hello!</Text> 
```

元素被呈现到平台的相应代码中(例如，成为 iOS 上的 UILabel)。

元素是不可变的，这意味着它们不会改变。如果一个元素的属性或子元素发生变化，那么一个新的元素将被创建并在其位置上呈现。这种更新只发生在改变的元素上，而不是整个树。

# 组件，&道具，儿童

React 的主要优势之一是它专注于将 UI 分割成独立的、可重用的称为组件的部分。

组件是接受输入(props)并返回描述 UI 的 React 元素的 JavaScript 函数或类。

**组件名称总是以大写字母**开头。这是为了在渲染到 DOM 时能够区分 DOM 标签和组件。

## 道具

Props 是传递给组件的输入，并且是只读的。

```
const element = <Welcome name="Chris" /> 
```

值“Chris”作为 **props.name** 传递给 props 对象中的组件 Welcome。

道具是组件可重用性的关键。

```
const welcome1 = <Welcome name="Alice" />
const welcome2 = <Welcome name="Bob" />
const welcome3 = <Welcome name="Chris" /> 
```

有一个名为 **children** 的特殊属性，它是在组件标签之间传递的元素。

```
const Card = (props) => {
    return (
        <View>
            <Text>{props.title}</Text>
            {props.children}
        </View>;
}

const MyCard = (props) => {
    return (
        <Card title='Cats'>
            <Image source='logo.png') />    // <--
            <Text>Content</Text> // <-^- Children
        </Card>
    );
} 
```

## 组件

### 基于类

基于类的组件曾经是需要保持状态的组件的唯一选择，但由于引入了 React 挂钩，它开始不受欢迎，React 挂钩赋予功能组件基于类的组件的所有功能，同时仍然保持它们的简单性。

```
class Welcome extends React.Component {
  render() {
    return <Text>Hello, {this.props.name}</Text>
  }
} 
```

> 人们发现功能组件比基于类的组件更容易使用的一个主要原因是不需要使用“this”。

### 功能性

功能组件实际上是作为 JavaScript 函数编写的组件。因为它是一个 JavaScript 函数，所以可以用经典的方式编写，或者作为一个箭头函数。

```
// Classic
function Welcome(props) {
    return <Text>Hello, {props.name}</Text>;
}

// Arrow
const Welcome = (props) => {
    return <Text>Hello, {props.name}</Text>;
};

// Arrow w/ Args Destructuring and Implicit return (Better)
const Welcome = ({ name }) => (
    <Text>Hello, {name}</Text>;
); 
```

# 状态

状态是组件封装的关键。它允许组件持有和管理自己的数据。

> **属性**由父设置，并且在组件的整个生命周期中不改变，而**状态**由组件处理，并且可以改变。

基于类的组件和功能组件使用状态的方式是两者的主要区别之一。

## 基于类的组件

```
class Counter extends Component {
  state = { count: 0 }

  render() {
    return (
      <View>
        <Text>{this.state.count}</Text>
        <Button
          onPress={() => this.setState({ count: this.state.count + 1 })}
          title="+"
        />
        <Button
          onPress={() => this.setState({ count: this.state.count - 1 })}
          title="-"
        />
      </View>
    )
  }
} 
```

## 功能组件

```
const Counter = props => {
  const [count, setCount] = useState(0)

  return (
    <View>
      <Text>{count}</Text>
      <Button onPress={() => setCount(count + 1)} title="+" />
      <Button onPress={() => setCount(count - 1)} title="-" />
    </View>
  )
} 
```

### 使用状态钩子

useState 挂钩使功能组件能够保持和管理状态。

`const [value, setValue] = useState(initialValue)`

# 挂钩&生命周期方法

对于高级逻辑，基于类的组件有生命周期方法，功能组件有钩子。

## 生命周期方法

[![Lifecycle Methods](img/3bcf65e61380c9ebe75840c3fc6482ec.png "Lifecycle Methods")](https://res.cloudinary.com/practicaldev/image/fetch/s--ESFzJXSb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thefinnternet.com/static/b0e04acf836d4df1220f1bb21b2472bf/c739e/react_lifecycle_diagram.jpg)

我们将用粗体字介绍这些方法，因为它们是最常用的。

### 构造函数

`constructor(props)`

> 如果不初始化状态，也不绑定方法，就不需要为 React 组件实现构造函数。

在安装组件之前调用该函数。

因为基于类的组件是 React 的子类。组件，在构造函数中，它应该在任何其他语句之前调用 **super(props)** 。否则，this.props 将在构造函数中未定义。

用于:

*   通过给这个状态分配一个对象来初始化本地状态
*   将事件处理程序方法绑定到实例。(**忘记绑定方法是使用类组件时的一个常见问题**

> 不应在构造函数()中调用 setState()。相反，如果您的组件需要使用本地状态，请在构造函数中将初始状态直接分配给 this.state。

```
constructor(props) {
  super(props);
  // Don't call this.setState() here!
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
} 
```

### 渲染

`render()`

**这是类组件**中唯一需要的方法

当需要呈现变化时，调用该函数。

render 函数用于返回您想要呈现的内容；React 元素、数组和片段、门户、字符串、数字、布尔值或 null。这个函数应该是纯**的**，这意味着它不修改组件状态，它每次被调用时都返回相同的结果，并且不会引起任何副作用。

```
render() {
    return (
        <View>
            <Text>{this.state.name}</Text>
            <Text>{this.props.status}</Text>
        </View>
    );
} 
```

### 组件装载

`componentDidMount()`

该函数在组件安装后立即被调用。

用于:

*   从远程端点加载数据
*   设置订阅(应该在 componentWillUnmount()中取消订阅)。

```
componentDidMount() {
    fetch('https://api.mydomain.com')
        .then(response => response.json())
        .then(data => this.setState({ data }));
} 
```

### componentDidUpdate

`componentDidUpdate(prevProps, prevState, snapshot)`

该函数在更新发生后立即被调用(不包括初始渲染)。

用于:

*   您希望在更新后发生的特定 UI 操作
*   应由状态或属性更改触发的网络请求

```
componentDidUpdate(prevProps) {
    if (this.props.searchTerm !== prevProps.searchTerm) {
        this.fetchData(this.props.searchTerm);
    }
} 
```

> 如果使用 setState()，则必须将其包装在条件中，否则将导致无限循环。

### componentWillUnmount

`componentWillUnmount()`

在卸载和销毁组件之前，立即调用此函数。

用于:

*   进行任何必要的清理(例如，取消请求、取消订阅等)。)

```
componentWillUnmount() {
    client.unsubscribe();
} 
```

## 挂钩

钩子是向功能组件添加功能的一种新方法。它的两大卖点是

*   从组件中提取有状态逻辑，这样就可以独立地测试和重用它，而不需要改变组件的层次结构。
*   用功能组件做任何事情

使用钩子只有两条规则

*   不要从内部循环、条件或嵌套函数中调用钩子
*   仅从 React 函数调用挂钩

### 使用状态

`const [value, setValue] = useState(initialValue);`

**这个钩子用于给一个功能组件添加状态。**

如果你想要多个状态变量，你只需要用不同的变量名再次调用 useState。另一个选择是使用另一个钩子，比如 *useReducer* ，它更适合处理更复杂的状态情况。

**值**保持*值*的当前状态，**设定值**是更新*值*的状态的函数。

```
const Counter = props => {
  const [count, setCount] = useState(0)

  return (
    <View>
      <Text>{count}</Text>
      <Button onPress={() => setCount(count + 1)} title="+" />
      <Button onPress={() => setCount(count - 1)} title="-" />
    </View>
  )
} 
```

### 使用效果

```
useEffect(() => {
  // execute side effects

  return function cleanup() {
    // execute side effect cleanup if necessary
  }
}, [onlyRunEffectIfThisValueChanges]) 
```

**该挂钩用于在功能组件中执行副作用。**

副作用是像数据获取、设置订阅和与外部资源交互这样的操作。这些是通常会在*组件中卸载*、*组件更新*和*组件卸载*的内容。同样，就像使用 *useState* 钩子一样，如果你有多个副作用，你可以用它们自己的 *useEffect* 来处理每个副作用。

这个钩子在第一次渲染时触发，在每一次渲染后触发(如果有清理功能，它被触发是为了在运行下一个之前清理前一个效果)。

注意事项

*   返回清理函数是可选的。如果你没有要清理的东西，就把退货扔掉。
*   您可以选择传入一个变量数组作为第二个参数，以指定该效果只在其中一个变量发生变化时运行。使用它来确保效果(和清理)仅在发生相关变化时再次触发。
*   如果您希望您的效果(和清理)只运行一次，请传递一个空数组([])作为第二个参数。

```
useEffect(() => {
  fetch("https://api.mydomain.com/search={searchTerm}")
    .then(response => response.json())
    .then(data => setResults({ data }))
}, [searchTerm]) 
```

### 自定义挂钩

如果您发现自己正在编写想要在多个组件中使用的逻辑，请考虑编写一个自定义钩子。自定义钩子只是一个 JavaScript 函数，它的名字以“use”开头，可以调用其他钩子。然后要使用它，您只需将其导入您的组件文件并相应地使用它。

# 有条件渲染

向 JSX 中添加一些简单的 JavaScript 逻辑可以为组件添加一些强大的条件呈现。

```
const Greeting = ({ user }) => {
  if (user) {
    return <Text>Greetings {user}</Text>
  } else {
    return <Text>Greetings anonymous</Text>
  }
}

// Using the ternary operator
// (condition ? true : false)
const Greeting = ({ user }) => {
  return <Text>Greetings {user ? user : "anonymous"}</Text>
}

// If you want to return nothing (null) when a condition
// is false then you can just use bitwise and (&&)
const Greeting = ({ user }) => {
    return {user && <Text>Greetings {user}</Text>};
} 
```

# 入门

好了，现在我们已经介绍了基础知识，我们可以继续在我们的手机上实际运行这些代码了！值得庆幸的是，有一个叫 Expo 的工具让设置变得简单&让我们可以在模拟器上或直接在我们的设备上轻松开发应用程序，而不必设置 XCode 或 Android Studio。

## 安装节点

[下载节点](https://nodejs.org/en/download/)

## 安装世博 CLI

`npm install -g expo-cli`

## 初始化项目

```
expo init MyProject
cd MyProject 
```

## 启动开发服务器

`npm start`或`yarn start`

这将启动开发服务器，您应该可以在打开的浏览器页面中看到它的状态。在此页面上，您可以在模拟器中启动应用程序，或者在您的设备上下载 Expo 客户端，扫描二维码即可下载并启动。