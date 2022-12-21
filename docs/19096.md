# 使用酶和 Jest 对高阶组件进行单元测试

> 原文：<https://dev.to/farskid/unit-testing-higher-order-components-with-enzyme-and-jest-4oli>

并非所有为 React 应用程序编写的组件都可以分为有状态和无状态(哑)类别。React 中还有第三种高级组件，称为高阶组件。高阶分量是将一个分量作为参数并返回另一个分量的函数。查看我的另一篇文章，了解高阶组件在现实世界中是如何使用的。

## 创建带有测试的特设

我们将使用 Enzyme 和 Jest，但是这些概念适用于任何测试库。

我为自己的每个项目写的一个常见 hoc 叫做`withConditional`。它的目的是当且仅当条件通过时才呈现组件，否则只返回 null。

```
import React from "react";
const withConditional = Component =>
  function withConditionalComponent({ condition, ...props }) {
    if (condition) {
      return <Component {...props} />;
    }
    return null;
  };
export default withConditional; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，当传递给 HOC 的条件评估为真时，它返回组件，否则，它返回`null`。

那么，如何对 HOC 进行单元测试呢？令人惊讶的是，很少有文章讨论对这些组件进行单元测试，我很难找到合适的方法。最近解决方案点击！

## 解

要正确测试这些糟糕的组件，您只需要知道它们是简单的函数，仅此而已！

`withConditional`的使用方式如下:

```
const ConditionalComponent = withConditional(MyComponent);
class HelloWorld extends React.Component {
  render() {
    return <ConditionalComponent condition={3 > 4} />;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们也应该使用完全相同的概念来对它们进行单元测试。

这里要考虑的重要事情是测试所有状态的组件。例如，如果我们的 HOC 可以处理两种不同的状态，这取决于传递给它的条件，那么我们必须在测试中考虑这两种状态。

## 当条件通过时:

```
it("should render the component only when the condition passes", () => {
  const ConditionalComponent = withConditional(Component);
  const wrapper = shallow(<ConditionalComponent condition={true} />);
  expect(wrapper.html()).not.toBe(null);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 当条件失败时:

```
it("should return null when the condition fails", () => {
  const ConditionalComponent = withConditional(Component);
  const wrapper = shallow(<ConditionalComponent condition={false} />);
  expect(wrapper.html()).toBe(null);
}); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

玩得开心，测试一下吧！