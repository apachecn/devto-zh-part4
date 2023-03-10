# ReactHooks:使用状态

> 原文：<https://dev.to/chriswcs/reacthooks-usestate-o4c>

## 功能组件中的状态

函数式反应组件是一个接受 props 作为参数的函数，它的名字以大写字母开头，返回 JSX。功能组件可以用箭头函数和标准函数编写。以下是一些功能组件的示例。

```
function Hello(props) {
  return <h1>Hello, {props.name}</h1> }

const Hi = ({name}) => <h2>Hi, {name}</h2> 
const Buttons = () => {
  return (
    <div>
      <button>Rain</button>
      <button>Snow</button>
    </div>
  )
}

function App() {
  return (
    <div>
      <Hello name={"World"}/>
      <Hi name={"Jim"} />
      <Buttons />
    </div>
  );
} 
```

在钩子发布之前，本地状态只能添加到类组件中。功能组件被称为表示组件，因为它们只能表示传递给它们的数据。

```
function CounterWithOutState() {
  return <button>0</button>; }

class Counter extends React.Component {
  state = { count: 0 };

  onClick = () => {
    this.setState({
      count: this.state.count + 1
    });
  };

  render() {
    return <button onClick={this.onClick}>{this.state.count}</button>;
  }
} 
```

类组件仍然受支持，并且没有计划将它们从 API 中移除。钩子的添加提供了一种不同的方式来添加状态，有些人可能会觉得更理想。注意钩子不能在类中使用。

## 使用状态 API

下面是一个 useState 钩子的例子。

```
function Counter() {
  const [count, setCount] = React.useState(0);
  const onClick = () => setCount(count + 1);
  return <button onClick={onClick}>{count}</button>; } 
```

初始状态被传递给 useState，并返回一个由两个事物组成的数组。数组中的第一项是对状态的引用，第二项是更新状态的函数。无论传递到函数中的是什么，在本例中称为 setCount，都将覆盖当前存储的状态。数组中的两个项目可以被命名为最适合情况的名称，这有助于更好地描述这两个项目。

也可以向 updater 函数传递一个函数，而不是新值。这个函数将把以前的值作为第一个参数传入。该函数将运行，返回值将是状态的新值。

```
 const onClick = () => setCount(prevCount => prevCount + 1); 
```

useState 挂钩可以在函数中多次使用。下面是两个不同的例子。一个使用 useState 一次，另一个使用 useState 两次。哪一个更好可能是个人偏好，然而两者都可能走向极端，代码将难以理解。

```
 // One useState

  const [state, setState] = React.useState({
    input: '',
    list: [],
  });
  const onChange = event => setState({
    ...state,
    input: event.target.value,
  })
  const onClick = () => setState({
    ...state,
    list: [...state.list, state.input]
  })

 // Two useStates

 const [input, setInput] = React.useState("initial");
 const [list, setList] = React.useState([]);

 const onChange = event => setInput(event.target.value)
 const onClick = () => setList([...list, input]) 
```