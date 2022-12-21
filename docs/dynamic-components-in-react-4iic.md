# React 中的动态组件

> 原文：<https://dev.to/arpit016/dynamic-components-in-react-4iic>

当我们谈论 react 中的动态组件时，我们通常会看到关于使用 react-loadable 延迟加载组件或在运行时延迟导入组件的帖子。但是，在这里我想分享一个不同的动态组件用例，这是我最近在一个项目中遇到的。

## 长若..else/开关..案例街区地狱

好的，我有一个用例，后端发送给我某种类型，基于这种类型，我必须呈现这种类型的特定组件。最初，它开始有两三个组件，但很快就发展到 10 个组件，甚至更多。是的，你可以想象我的代码块看起来会是什么样子，大概是这样的:

```
 function List(props.type) {
 switch(type) {
  case "type1": {
    return (
      <Type1 />
    )
  }
  case "type2": {
    return (
      <Type2 />
    )
  }
  case "type3": {
    return (
      <Type3 />
    )
  }
  .
  .
  .
  .
  case "type10": {
    return (
      <Type10 />
    )
  }
  default: {
    return (
      <BasicType />
    )
  }
 }
} 
```

正如我们所看到的，这对于编写、更新和维护来说已经变得太麻烦了，我需要的是从我从后端接收的类型中解析组件，并以一种更优雅的方式返回该组件。

我开始看 react 文档，这是我第一个去的地方，因为文档写得非常好。在那里，我找到了解决问题的方法。这一切都归结于 React 的基础。

根据文件显示，

*当一个元素类型以小写字母开始时，它指的是一个内置组件，如 or 并产生一个传递给 React.createElement 的字符串“div”或“span”。以大写字母开始的类型，如 compile to react . createelement(Foo ),对应于 JavaScript 文件中定义或导入的组件。*

**我们建议用大写字母命名组件。如果您确实有一个以小写字母开头的组件，在 JSX 中使用它之前，请将它赋给一个大写的变量。**

 ** * ** 

基本上，如果元素类型以小写字母开头，那么 react 会在默认的 html 标记中查找它，如果元素类型以大写字母开头，它会查找自定义定义和导入的组件。

因此，我所要做的就是解析组件类型，并将其赋给一个大写的变量，然后返回该组件。在医生的帮助下，我做到了这一点:

* * *

```
 import React from 'react';
import { Type1, Type2, Type3, Type4, Type5,...,Type10, BasicType } from './Types';

const components = {
  basic: BasicType,
  type1: Type1,
  type2: Type2,
  type3: Type3,
  type4: Type4,
  type5: Type5,
  .
  .
  .
  type10: Type10
};

function List(props) {

  // In this line I am resolving the component dynamically at runtime and 
  // assigning it to a capitalized Variable and then I return that 
  // component with whatever extra custom props, methods I want to pass 
  // in it.

  const TypeComponent = components[props.type.toLowerCase() || 'basic'];
  return (
   <TypeComponent content={props.content} actions={props.actions} />
  );
} 
```

请注意，我将 **props.type** 值传递给了 components 对象，如果键匹配，该对象将返回一个组件值，并将其分配给 **TypeComponent**

**【props . type . to lower case()| | ' basic ']**这从我这边来说无非是额外的安全。我要确保类型是小写的，如果什么都没有找到或为空，那么默认情况下将它解析为“basic”。

我的长**开关/外壳块地狱**被简化为上面这段优雅的代码，它易于维护和更新。

您可以在以下链接的 react 文档中了解更多信息:

*   [https://react js . org/docs/jsx-in-depth . html #用户定义组件-必须大写](https://reactjs.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized)

*   [https://react js . org/docs/jsx-in-depth . html #选择运行时类型](https://reactjs.org/docs/jsx-in-depth.html#choosing-the-type-at-runtime)

感谢您花时间阅读这篇文章！如果它对你有用，如果你认为它可能对其他人有用，请发微博并分享这篇文章。