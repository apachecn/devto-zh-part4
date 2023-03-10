# 如何将 data-*属性添加到组件库中，并在 E2E 测试中从中受益

> 原文：<https://dev.to/marcostreng/how-to-add-data-attributes-to-your-component-library-and-benefit-from-them-in-your-e2e-tests-148l>

## [T1】简介](#intro)

当构建一个组件库时，最重要的事情之一是一个组件的可用属性的无缝和清晰的文档。

你的组件库的用户不应该能够在它不使用的组件上写随机属性。否则会很快抛出类似`Warning: React does not recognize the 'isVisible' prop on a DOM element. [...] If you accidentally passed it from a parent component, remove it from the DOM element.`的错误。

## 数据-*以及为什么我们有时需要它

在一些用例中，用户需要能够在组件上传递某种随机属性。

对我来说，这样一个用例是为我的项目中的库组件添加像`data-test-element="header"`这样的两端测试的选择器属性。
(顺便说一句:我喜欢用[柏树](https://www.cypress.io/)写我的 E2E 测试。)

## 解

一个小而简单的函数，它接受你的属性对象并返回应该出现在显示的标记中的有效属性。

```
const validateProps = (props) => {
  const newProps = {}

  Object.keys(props).forEach((key) => {
    if (key.startsWith('data-') || key === 'className') newProps[key] = props[key]
  })

  return newProps
} 
```

Enter fullscreen mode Exit fullscreen mode

**用途**

在您的组件中，您可以提取它自己使用的属性。所有其他属性都通过`validateProps()`传递。

```
const Badge = ({ onToggle, isActive, children, ...props }) => (
  <Wrapper
    isActive={isActive}
    onClick={onToggle}
    {...validateProps(props)}
  >
    {children}
  </Wrapper> ) 
```

Enter fullscreen mode Exit fullscreen mode

*？？？类名称？？？*

也许你会奇怪为什么`validateProps()`会保留`className`属性？那是因为我和[风格化组件](https://www.styled-components.com)一起工作。如果你从外部给一个组件添加一些自定义样式，你必须在组件上写这个属性(见[样式-组件文档](https://www.styled-components.com/docs/advanced#existing-css))

## 生产与开发

根据您运行测试的方式和位置，您可能不希望在生产中的标记中看到那些`data-test-*`属性。

这种情况下我推荐[babel-plugin-react-remove-properties](https://github.com/oliviertassinari/babel-plugin-react-remove-properties)。

**用途**

仅在生产中使用项目的`.babelrc`中的以下配置过滤所有`data-test-element`属性。

```
{  "env":  {  "production":  {  "plugins":  [  [  "react-remove-properties",  {  "properties":  [  "data-test-element"  ]  }  ]  ]  },  }  } 
```

Enter fullscreen mode Exit fullscreen mode