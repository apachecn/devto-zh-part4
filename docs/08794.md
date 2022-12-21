# 使用 useRef 挂钩存储元素数组

> 原文：<https://dev.to/mattc/adding-react-refs-to-an-array-of-items-3lik>

使用 useRef 钩子
React refs 存储一个元素数组对于我们在用户与元素交互之后必须将焦点设置到元素上是很有用的。在我的例子中，当我选择/使用箭头键时，我需要使用 refs 将焦点设置到正确的 accordion 项。

在这样做的时候，我面临的问题是将 ref 传递到我的组件中的问题，以及让一个 ref 表示一个项目数组的问题，就像在 none react 应用程序中使用 document.querySelectorAll()一样。

## 问题

当只有一个 ref 时，我们倾向于简单地用创建 useRef 函数的变量来分配 ref 值。

```
const item = useRef(null)

<div ref={item}/> 
```

Enter fullscreen mode Exit fullscreen mode

在我的用例中，我不能简单地将我的 ref 分配给元素，因为在渲染过程中 ref 是在地图中分配的。这意味着数组中的最后一项将是分配给 item.current 的元素。

## 解

ref prop 将元素作为参数提供给我们，允许我们按照自己的意愿分配元素。在本例中，我们将该元素放入 itemsEls.current 数组中，以便在以后的工作中使用。

```
const itemEls = useRef(new Array())

{items.map(item => (
 <p key={item} ref={(element) => itemEls.current.push(element)}>{item}</p> )) 
```

Enter fullscreen mode Exit fullscreen mode

注意:同样的方法也适用于 id 作为键的对象。

```
const itemEls = useRef({})

{items.map((item, index)) => (
 <p key={item} ref={(element) => itemEls.current[index] = element}>{item}</p> )) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们现在可以在一个变量中存储一个 refs 数组，以便在用例出现时使用。

注意:这个将 ref 赋给 p 标签的例子纯粹是为了简化代码。一个很好的用例是，当用户与组件(如 Accordion/Tabs/Modal)交互时，构建需要应用焦点的东西。

```
import React, { useRef, useEffect } from 'react'
export const Component = ({ items }) => {
  const itemsEls = useRef(new Array())

  return (
    {items.map((item) => {
      const getRef = (element) => (itemsEls.current.push(element))
      return <p key={getRef}>{item}</p>
    })}
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的阅读。