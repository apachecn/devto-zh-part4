# 反应中的高阶成分(hoc ),何时反应，为什么反应

> 原文：<https://dev.to/itminds/higher-order-components-hocs-in-react-when-and-why-1ipg>

React 中的高阶组件(hoc)是为组件逻辑引入可重用性的一种方式。听起来不错，让我们开始使用 HOCs 吧。现在等一下，在你开始在你的应用程序中引入 HOCs 之前，所有这些通用的优点都有一些缺点。

目前我在用 React 和 React-Native 编写的大规模平台上工作。React-Native app 以用户为中心，React app 以控制 app 中显示的内容和管理连接到平台的用户为中心。

网络平台在我工作的这一年里增长了很多，现在已经有 300000 多行代码。

从开发的开始，就有一种围绕创建通用的可重用组件来保持干燥的驱动力。虽然这在本质上是非常好的，但我们发现，通过开发，有时我们代码的通用性会让我们搬起石头砸自己的脚。

我们最初打算让 hoc 处理对 API 的所有数据访问，这样我们就可以把它从视图逻辑中分离出来。虽然这本质上是一个好主意，但它引入了一些意想不到的副作用，因为参数化数据访问是用我们的 HOC 中的额外道具处理的，并且破坏了 HOC 的可组合性，因为多个 HOC 可能需要相同的参数，并且这些参数不会被解析。

用道具写 HOC 的约定是

```
render() {
  const { extraProp, ...passThroughProps } = this.props;

  return (
    <WrappedComponent
      {...passThroughProps}
    />
  );
} 
```

它删除了 HOC 所需的额外道具，并将其余的传递给包装的组件。但是如果你写了两个或者更多的 hoc，都需要额外的道具，像这样

```
const enhance = compose(
  withSomeData, // requires extraProp
  withUpdateSomeData, // requires extraProp
);

export const EnhancedComponent = enhance(Component); 
```

只有第一个会得到额外的道具。

代替这种方法，你可以把获得额外道具的责任转移到包装的组件上，就像这样

```
const withSomeData = (getExtraProps) => (WrappedComponent) => {
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        data: DataSource.get(getExtraProps(props))
      };
    }

    render() {
      return <WrappedComponent {...this.props} data={this.state.data} />
    }
  }
}

// ... And something similar for withUpdateSomeData

// And now we are able to use it like so

const enhance = compose(
  withSomeData(props => props.extraProps),
  withUpdateSomeData(props => props.extraProps),
);

export const EnhancedComponent = enhance(Component); 
```

这允许 hoc 是可组合的，即使它们需要相同的额外道具，但是这将责任转移给了包装的组件，这当然是一种折衷。