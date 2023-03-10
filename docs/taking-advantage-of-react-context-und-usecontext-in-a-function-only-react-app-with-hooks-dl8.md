# 利用函数中的 React context und useContext 只有 React App 有钩子

> 原文：<https://dev.to/svschannak/taking-advantage-of-react-context-und-usecontext-in-a-function-only-react-app-with-hooks-dl8>

## React 中的上下文解决什么问题？

React 中上下文的典型用例是传递用户数据或结帐中当前商品的数量，因为您不需要在每次重新呈现组件时都加载这种数据。

在一个常见的场景中，您将从保存数据的主根目录传递值。这就导致了一个问题，您使用一些子组件作为代理来传递值。在这种情况下，您将向组件添加额外的属性和依赖项，即使您并不真正需要它们。

```
import React from 'react';

function UserData({ userName }) {
    return (
        <div>{userName}</div>
    )
}

function Header({ userName }) {
    return (
        <div>
            <h2>Header</h2>
            <UserData userName={userName} />
        </div>
    )
}

export default function MainFn() {
    const [userName, setUserName] = React.useState("Sven");
    React.useEffect(() => {
        // load user data and set username
    }, []);

    return (
        <Header userName={userName} />
    )
} 
```

## 与 React 上下文的更好方式

钩子和反应上下文来救援！使用 Context，我们可以从 MainFn 中设置一些数据，而使用 useContext，我们可以将上下文数据放入任何子元素中。

```
import React from 'react';

const UserContext = React.createContext(
    { username: "", email: "" }
);

function UserData() {
    const context = React.useContext(UserContext);

    return (
        <div>
            {context.username}
        </div>
    )
}

function Header() {
    return (
        <UserData />
    )
}

export default function MainFn() {
    const [username, setUsername] = React.useState("Sven");
    const context = React.useContext(UserContext);

    // copy the origin context to override only necessary objects and not the whole object
    const contextValue = { ...context }
    contextValue["username"] = username;

    return (
        <UserContext.Provider value={contextValue}>
            <Header />
        </UserContext.Provider>
    )
}; 
```

## 免责声明

要经常思考是否真的要用到上下文。如您所见，UserData 的函数没有指定任何参数。所以你永远不知道需要什么数据，数据从哪里来。通过设计，很难看出在使用上下文的组件中需要哪些依赖关系。