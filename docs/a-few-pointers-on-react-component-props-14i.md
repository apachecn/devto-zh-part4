# 关于 React 组件道具的几点建议

> 原文：<https://dev.to/beccaliz/a-few-pointers-on-react-component-props-14i>

作为 React 开发人员，我们首先要学习的是如何使用 props 将参数传递到组件中。然而，在设计组件的 API 时，我们经常会遇到一些常见的陷阱，这使得我们的组件更难使用和测试。这里有一些提示，可以帮助我们编写更有用的组件。

像考虑共享库的 API 一样考虑 React 组件的 API。如何让另一个用户(或你自己)尽可能容易地做好这件事？

## 所需道具

你的组件需要尽可能少的道具。例如，布尔属性不应该是必需的，应该默认为 false。我们在 HTML 规范中看到了这种模式，应该在我们的组件中模仿它。

```
<MyButton disabled={false}/>
<MyButton disabled={true}/>

// vs

<MyButton />
<MyButton disabled /> 
```

此外，我们应该为任何不影响组件行为的道具提供合理的默认值。

```
const MyButton = ({ color: color.primary }) => {
  ...
} 
```

## 数据结构

有时候，我会看到这样的道具类型。

```
// JS

MyComponent.propTypes = {
  data: PropTypes.object.isRequired
} 
```

如果我使用这个组件，我不可能一眼就看出这个道具的结构是什么。我需要通读`render`方法中的逻辑，然后抱最好的希望。

你可以给`data`一个更具体的类型，就像这样。

```
MyComponent.propTypes = {
  data: PropTypes.shape({ 
    value: PropTypes.string,
    id: PropTypes.number,
  }).isRequired,
} 
```

但是更好的方法可能是将这种数据结构扁平化。

```
MyComponent.propTypes = {
  value: PropTypes.string,
  id: PropTypes.number,
} 
```

如果您正在处理格式化的 API 数据，那么为了在较低层次的表示组件中具有可读性，可以在顶层对这些数据进行[重新格式化。](https://dev.to/beccaliz/react-refactoring-tips-dysfunctional-api-boundaries-1gib)

如果你让道具尽可能简单易读，我保证你以后会感谢自己的。