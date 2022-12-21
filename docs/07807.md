# React 应用程序中的静态类型

> 原文：<https://dev.to/dmitrykotlyarenko/static-typing-in-a-react-application-kij>

2016 年， [TypeScript](https://www.typescriptlang.org/) 开始重新流行起来。开发人员使用这种编程语言完全重写了许多流行的技术，并向现有平台添加了静态分析工具。使用这样的全局过程为成千上万个项目的代码库增加了稳定性。

我为什么选择 React 做 app 开发？今天，这个库无疑超过了它的竞争对手。世界上最大的开发者社区已经在 [React](https://en.wikipedia.org/wiki/React_(JavaScript_library)) 附近形成。每三个 SPA 就有一个写在这个平台上。此外，有许多伟大的[项目](https://clever-solution.com/case-studies/problems-of-development-the-legacy-mobile-project)涉及 React Native 的使用，这是一个基于 React.js 的 iOS、UWP 和 Android 应用开发平台

两个超级流行的工具 TypeScript 和 React 的集成为应用程序开发人员提供了巨大的机会。

# 例子

首先，让我们弄清楚 React 可以使用哪些类型。我们将从基础开始，然后向功能组件添加类型。

```
import * as React from 'react';

const HelloWorld: React.FunctionComponent<{
  name: string;
}> = ({ name = 'World' }) => {
  return <div>Hello, {props.name}</div>;
};

export default HelloWorld; 
```

对于功能组件(或无状态组件)，我们必须使用“反应”的定义。FunctionComponent "类型。我们还可以为“Props”参数定义类型，即父节点传递给组件的字段。在这种情况下，“Props”只能包含一个类型为“string”的“name”字段。

所有这些看起来并不复杂。类组件呢？

```
import * as React from 'react';

interface State {
  name: string;
}

interface Props {}

class HelloWorld extends React.Component<Props, State> {
  state = {
    name: 'World'
  }

  setName(name: string) {
    this.setState({ name });
  }

  redner() {
    return (
      <React.Fragment>
        <h1>Hello, {this.state.name}</h1>
        <input value={this.state.name} onChange={(e) => this.setName(e.target.value)} />
      </React.Fragment>
    );
  }
} 
```

在“class”的例子中，我们创建了两个接口:Props 和 State。在他们的帮助下，我们确定了传入 props(空)的签名和组件状态的签名，就像在功能组件的例子中一样。我们还可以添加默认的道具值。

```
import * as React from 'react';

interface Props {
  name?: string;
}

export default class HelloWorld extends React.Component<Props> {
  static defaultProps: Props = {
    name: 'World'
  };

  render () {
    return <h1>Hello, {this.props.name}</h1>;
  }
} 
```

仅此而已！我们的小 React 应用程序已经在组件的参数和状态值级别进行了严格的类型化。

让我们考虑一下优势:

*   我们可以在编译阶段看到所有的类型不匹配；
*   一个适当配置的编辑器将通过简单地突出签名或数据类型之间的差异来帮助我们避免错误(甚至在开发阶段)；
*   我们有关于接口和类型定义的文档。

# [参数中的](#enum-in-parameters)枚举

枚举是一种枚举数据类型。如果我们将这个类型添加到一个变量或接口字段，那么这个字段或变量的值将等于 Enum 中的特定值。
例如:

```
import * as React from 'react';

enum Colors {
  RED,
  BLUE,
  GREEN
}

const ColorResult: React.FunctionComponent<{
  color: Colors;
}> = ({ color = Colors.Red }) => {
  return <div>Your color is {props.color}</div>;
};

export default ColorResult; 
```

在已经熟悉的功能组件中，我们可以显示用户选择的颜色。在“enum Colors”类型中，我们应该指定可以传输到组件的所有可能的颜色选项。如果 TypeScript 编译器发现类型不匹配，它将显示错误警告。

# 严格还原

今天，我们仍然有许多应用程序运行在 Redux 上。TypeScript 可以改进它们。

```
import * as React from 'react';

const initialState = { name: 'World' };
type HelloWorldStateProps = Readonly<typeof initialState>;

interface Action {
    type: string;
  name?: string;
}

const worldNameReducer = (
    state: HelloWorldStateProps = initialState,
    action: Action
): HelloWorldStateProps => {
    switch (action.type) {
        case "SET":
            return { name: action.name };
        case "CLEAR":
            return { name: initialState.name };
        default:
            return state;
    }
};

const set = (name): Action => ({ type: "SET", name });
const clear = (): Action => ({ type: "CLEAR" });

const store = createStore(
    combineReducers({
        world: worldNameReducer
    })
);

type StateProps = ReturnType<typeof mapStateToProps>;
type DispatchProps = typeof mapDispatchToProps;

interface AppProps extends StateProps, DispatchProps {}
interface AppState extends StateProps {}

class App extends React.Component<AppProps, AppState> {
  state = {
    name: initialState.name
  }

  setName(name: string) {
    this.setState({ name });
  }

    render() {
        const { set, clear, name } = this.props;
        return (
            <div>
                <h1>Hello, {name}</h1>
        <input value={this.state.name} onChange={(e) => this.setName(e.target.value)} />

        <button onClick={() => set(this.state.name)}>Save Name</button>
        <button onClick={() => clear()}>Clear</button>
            </div>
        );
    }
}

const mapStateToProps = ({ world }: { world: HelloWorldStateProps }) => ({
    name: world.name,
});

const mapDispatchToProps = { set, clear };

const AppContainer = connect(
    mapStateToProps,
    mapDispatchToProps
)(App);

render(
    <Provider store={store}>
        <AppContainer />
    </Provider>,
    document.getElementById("root")
); 
```

在这个例子中，我一次向几个应用程序级别添加了类型。首先，我和减速器一起工作。reducer 接受输入上的“Action ”,并且应该返回对应于“HelloWorldStateProps”类型的对象。考虑到普通应用中大量的减速器，这是一个非常有用的创新。每个动作都有严格的“动作”签名。

输入的下一个层次是组件。我已经对 AppProps 和 AppState 应用了类型继承。如果具有这种签名的数据类型已经存在，我为什么还要编写更多呢？使用这种方法，维护系统就容易多了。如果某些元素改变了，所有的继承人也会改变。

# 结论

TypeScript 是一种运行在 JavaScript 之上的非常有用的语言。与 React 一起，它为前端应用程序提供了真正令人印象深刻的编程实践。