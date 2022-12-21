# JS Bites: React 钩子在一个既不是 React 函数也不是自定义 React 钩子的函数中被调用

> 原文：<https://dev.to/ranewallin/js-bites-react-hook-is-called-in-a-function-which-is-neither-a-react-function-or-sic-a-custom-react-hook-1g2c>

所以，你决定投身于 React Hooks。事情一直进行得很好(希望如此),直到你得到一个类似下面的神秘错误。

```
import React, { useState } from 'react';

const landingPage = ({ user }) => {
  const [user, setUser] = useState(user);

  return (
    <div>
      <span> Your users is </span> { user.name }
    </div>
  );
}

export default landingPage; 
```

Enter fullscreen mode Exit fullscreen mode

`Error: React Hook "useState" is called in a function "landingPage" which is neither a React function or a custom React Hook function.`

哦不！发生了什么事？抛开错误消息中糟糕的语法不谈，还有什么地方出错了呢？它看起来确实像一个 React 组件。您已经导入了`React`。您已经导入了`useState`。您正在导出您的函数。一切都应该正常，但事实并非如此。

这里有一个问题，当你使用 React 钩子时，你的功能组件的名字*必须*以大写字母开头。在这种情况下，函数名为`landingPage`。如果您将其更改为`LandingPage`，它将按预期工作。

同样，如果您要在钩子自定义钩子中使用钩子，那么自定义钩子的名称*必须*以“use”开头(小写)。

如果你想知道为什么，查看一下关于这个主题的 [React 文档](https://reactjs.org/docs/hooks-rules.html)。

* * *

```
 JS Bites

 Have you ever need a quick solution to a problem, but when you search 
 Google you are met with pages of tutorials and detailed instructions that 
 bury the information you need? That's what JS Bites attempts to solve. 
 Each post is a small, bite-sized primer on a very specific JS topic. The 
 aim is to provide enough detail to boost understanding, but not so much that 
 you become overwhelmed or lost. 
```

Enter fullscreen mode Exit fullscreen mode