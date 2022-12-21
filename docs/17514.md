# React 中的组件组合和继承

> 原文：<https://dev.to/fosteman/react-component-composition-inheritance-25i8>

# 组件组成

每当 UI 组件呈现其子组件时，`props.children`被**插入**标签:

```
const Bar = props => (
    <div className={'ui items ' + props.color}>
        {props.children}
    </div>
); 
```

孩子可以是任何东西:文本、表达式、元素、组件

```
...
const NavigationBar = props => (
    <Bar>
        My Bar Element! //text

        {props.items[Math.floor(Math.random() * props.items.length)]} //expression

        <a className="link">
            Anchor element
        </a>

        <Link>
            Custom component <Link>
        </Link>
    </Bar>
); 
```

在渲染`NavigationBar`之前，评估`Bar`的内部内容。

###### 注意，像`<Bar />`和`<NavigationBar />`这样反应元素与`<div />`或`<a />`的元素没有什么不同，因为库和巴别塔出于兼容性考虑将把 JSX 向下转换到 ES5。

## 为什么不是组件继承？

组件继承层次的创建可能会在可重用的非 UI 组件中找到它的用途，但是，功能组件最好提取到单独的 JavaScript 模块中。

感谢您的阅读！