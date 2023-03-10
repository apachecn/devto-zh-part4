# Reason React 导航中标题按钮和屏幕之间的通信

> 原文：<https://dev.to/idkjs/communitication-between-a-header-button-and-screen-in-reason-react-navigation-3dp6>

这篇文章将基于 [React 导航文档在这里](https://reactnavigation.org/docs/en/header-buttons.html#header-interaction-with-its-screen-component)讨论标题与屏幕组件的交互。

*在[https://expo.io/@idkjs/ReasonHeaderScreenDemo](https://expo.io/@idkjs/ReasonHeaderScreenDemo)T3】看结果*

这是来自[这里](https://reactnavigation.org/docs/en/header-buttons.html#header-interaction-with-its-screen-component)T3】的`js`版本

```
class HomeScreen extends React.Component {
  static navigationOptions = ({ navigation }) => {
    return {
      headerTitle: <LogoTitle />,
      headerRight: (
        <Button
          onPress={navigation.getParam('increaseCount')}
          title="+1"
          color="#fff"
        />
      ),
    };
  };

  componentDidMount() {
    this.props.navigation.setParams({ increaseCount: this._increaseCount });
  }

  state = {
    count: 0,
  };

  _increaseCount = () => {
    this.setState({ count: this.state.count + 1 });
  };

  /* later in the render function we display the count */
} 
```

在 ReasonML 版本中，我不知道如何与递增按钮模块共享`HomeScreen`状态的值。我从医生那里得到了暗示，他说:

> React Navigation 并不保证您的屏幕组件会在 header 之前安装。因为 increaseCount 参数是在 componentDidMount 中设置的，所以我们可能无法在 navigationOptions 中使用它。这通常不是问题，因为如果回调为空，按钮和可触摸组件的 onPress 将什么也不做。如果您在这里有自己的自定义组件，您应该确保它的行为符合预期，其 press handler 属性为 null。
> 
> 作为 setParams 的替代方法，您可以使用一个状态管理库(比如 Redux 或 MobX ),并以与两个不同组件相同的方式在标题和屏幕之间进行通信。

所以我想我需要某种状态管理解决方案，这是我目前从未使用过的。我记得[ [@ken_wheeler](https://dev.to/ken_wheeler) 说过[无状态](https://github.com/jamiebuilds/unstated-next)有多棒，所以我查了一下 github，看看有没有人在`react-native`中试过。我找到了 [@@mirshko](https://mobile.twitter.com/mirshko) 的[无铅回购](https://github.com/mirshko/unleaded)并按照我需要的指导去做。在四处寻找的时候，我发现[被](https://github.com/Raincal/re-unstated-next/blob/master/example/Index.re)[鱼枷](https://mobile.twitter.com/cyj228)重新声明-下一个，它在 ReasonML 中实现了`unstated`。你可以发现实施了  。

无论如何，这是我如何按照文档的建议使用一个库来共享`state`的。我创建了`CounterState.re`模块来存放我的计数器的状态。

这是直为[再陈述——下一个](https://github.com/Raincal/re-unstated-next/blob/master/example/Index.re)

```
type counter = {
  count: int,
  decrement: unit => unit,
  increment: unit => unit,
};

let useState = initial => {
  React.useReducer((_, action) => action, initial);
};

let useCounter = (~initialState=0, ()) => {
  let (count, setCount) = useState(initialState);
  let decrement = () => setCount(count - 1);
  let increment = () => setCount(count + 1);
  {count, decrement, increment};
};

module Counter =
  UnstatedNext.CreateContainer({
    type state = int;
    type value = counter;
    let useHook = useCounter;
  }); 
```

在接下来的`HomeScreen`模块中，我用`open CounterState`打开了那个模块，然后为我想传递给`HomeScreen`模块中`NavigationOptions`中`headerRight`的按钮创建了一个`IncButton`模块。我这样做是因为我需要使用`CounterState`的`useContainer`函数，并且不想直接在`headerRight`中计算传递。所以相反，我创建了按钮模块并传递给了`headerRight`。

这是右边标题中的`IncButton`,它访问状态容器。

```
module IncButton = {
  [@react.component]
  let make = () => {
    let counter = Counter.useContainer();
    <Button title="+" color="#fff" onPress={_ => counter.increment()} />;
  };
}; 
```

然后你把它传给`headerRight`像这样:

```
 make->NavigationOptions.(setNavigationOptions(t(
    // ~title="Home",
    // headerTitle instead of title
      ~headerTitle=NavigationOptions.HeaderTitle.element(<LogoTitle />),
      ~headerRight=<Button title="Info"
        color="#fff" onPress={_e =>
          Alert.alert(~title="This is a button!", ());
        }
      />,
     ()))); 
```

在`HomeScreen`模块中，我们用这一行在 ui 中呈现当前计数，在这里我们访问`Counter.useContainer`上的计数值。

```
...
<Text>
  {"Count: " ++ string_of_int(Counter.useContainer().count) |> React.string}
</Text>
... 
```

让这一切工作的诀窍是确保所有这些模块都可以访问同一个状态容器。我通过将我的`AppContainer`包装在状态容器的提供者包装器`<Container.Provider>`中来做到这一点。

```
@react.component]
let make = () =>
  <Counter.Provider initialState=0> <AppContainer /> </Counter.Provider>; 
```

看起来是这样的:

[![Alt Text](img/dce40e8aa4523fe643cc18c71ae03c32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rTSo9-M2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7w8wdlzmx0pfb9n2zce2.gif)

给我看看[码](https://github.com/idkjs/HeaderButtonScreenInteraction)。