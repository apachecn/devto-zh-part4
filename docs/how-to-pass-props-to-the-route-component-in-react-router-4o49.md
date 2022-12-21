# 如何向 React 路由器中的路由组件传递 props

> 原文：<https://dev.to/paramharrison/how-to-pass-props-to-the-route-component-in-react-router-4o49>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

我们已经在 react 路由器中看到了几个例子和用例。其中之一是将道具直接传递给路由组件。

这在 react router 中很容易实现，让我们创建一个新的路径来将道具传递给组件。

```
// App.js
...

const PropsPage = () => { return ( <h3>Props Page</h3> );};
const App = () => {
  return (
    <section className="App">
      <Router>
        ...
        <Link to="/404-not-found">404</Link>
        <Link to="/props">Passing Props</Link> <Switch>
          ...
          <Route exact path="/props" component={PropsPage} /> <Route component={NoMatchPage} />
        </Switch>
      </Router>
      <a href="/about">about with browser reload</a>
    </section>
  );
};

export default App; 
```

现在，我们刚刚添加了组件和新页面。让我们向页面传递一个道具`title`。

通过道具有两种方式。第一种是简单方法，

## 传球功能作为组件道具出现在路由组件中

```
...

const PropsPage = ({ title }) => {
  return (
    <h3>{title}</h3>
  );
};

...

<Route exact path="/props-through-component" component={() => <PropsPage title={`Props through component`} />} /> 
```

这是可行的，但 react 路由器不推荐这种方式。为什么？因为

*   在内部，react router 使用`React.createElement`来渲染传递给组件 props 的组件。如果我们向它传递一个函数，它将在每次渲染时创建一个新的组件，而不仅仅是更新现有的组件。
*   在页面相当简单的小应用程序中，这不会影响性能。但是对于在页面中有多个状态变化的大型应用程序，由于不必要的重新渲染，会降低页面的性能。

React 路由器为这种情况提供了一个简单的解决方案。不用通过`component`道具传递函数，我们可以通过`render`道具传递。在传递我们自己的道具的同时，我们还需要通过 react 路由器将默认的道具发送给渲染道具。让我们看看我们的例子，

```
...

<Link to="/props-through-render">Props through render</Link> ...

<Route exact path="/props-through-render" render={(props) => <PropsPage {...props} title={`Props through render`} />} /> 
```

这是 react 路由器中传递道具的推荐方式。

就这么简单。如果你想看完整的例子，请点击这里。

[https://codesandbox.io/embed/props-in-component-react-router-g2im5](https://codesandbox.io/embed/props-in-component-react-router-g2im5)

你可以查看这个系列的代码库[这里](https://github.com/learnwithparam/react-router-series)和这个部分的代码[这里](https://github.com/learnwithparam/react-router-series/commit/45efaedd0f0b2e74f231fe131f1bea327a0a96ac)