# 提供商模式

> 原文：<https://dev.to/fosteman/provider-pattern-4lh8>

# 提供者模式

React 功能上下文和子元素支持实现提供者模式。本质上使属性在提供组件的上下文中可访问。

## 上下文 API

这是一个强大的功能，它消除了钻取的混乱，例如，主题属性，沿着组件树到每个组件。强制属性与 childContextTypes 对象一起放置，描述组件的要求。

在组件中，您提供上下文。在这种情况下，它是一个硬编码的彩色主题属性，但是它可以是从组件状态到组件属性的任何内容。组件 A 显示组件 D，但是使上下文对它的所有子组件都可用。在组件 C 中，组件 D 下面的某个地方，您可以使用上下文对象。注意，组件 A 不需要通过 props 中的组件 D 传递任何东西。

```
class A extends React.Component { 
    getChildContext() {
      return {
        coloredTheme: "green"
      }; 
    }
    render() { 
      return <D />;
    } 
}
A.childContextTypes = {
      coloredTheme: PropTypes.string
};
class C extends React.Component { 
        render() {
          return (
            <div style={{ color: this.context.coloredTheme }}>
                  {this.children} 
            </div>
        ); 
        }
}
C.contextTypes = {
      coloredTheme: PropTypes.string
}; 
```

通过使用 this.context 中的彩色主题属性，组件可以派生其样式。这样，每个需要根据彩色主题进行样式化的组件都可以从 React 的上下文对象中获得必要的信息。

提供者组件将这样实现:

```
class ThemeProvider extends React.Component { 
    getChildContext() {
      return {
             coloredTheme: this.props.coloredTheme
             }; 
    }
    render() {
      return <div>{this.props.children}</div>;
    } 
}
ThemeProvider.childContextTypes = {
      coloredTheme: PropTypes.string
}; 
```

Provider 组件仅从传入的道具中设置彩色主题。此外，它还呈现其子代。

在声明提供者组件之后，您可以在 React 组件树中的任何地方使用它。但最有意义的是在组件层次结构的顶层使用它，使每个人都可以访问上下文，即彩色主题。

```
const coloredTheme = "green";
    // hardcoded theme
    // however, imagine the configuration is located somewhere else // and would be different for every user of your application // it would need to be fetched first
    // and varies depending on the app user
ReactDOM.render(
      <ThemeProvider coloredTheme={coloredTheme}>
        <App />
      </ThemeProvider>,
      document.getElementById('app')
); 
```

现在，每个子组件都可以使用 ThemeProvider 组件提供的彩色主题。它不必是直接子组件，在本例中是 App 组件，而是组件树中的任何组件。

```
class App extends React.Component {
  render() {
    return ( 
    <div>
      <Paragraph>
      That 's how you would use children in React 
      </Paragraph> 
    </div>
    );
  }
}
class Paragraph extends React.Component {
    render() {
      const {coloredTheme} = this.context;
      return ( 
        <p style={{color: coloredTheme}}> 
          {this.props.children} 
        </p> 
        );
      }
    }
Paragraph.contextTypes = {
      coloredTheme: PropTypes.string
}; 
```

这就是提供者模式的基本内容。您有使属性在 React 的上下文中可访问的提供者组件和使用上下文的组件。