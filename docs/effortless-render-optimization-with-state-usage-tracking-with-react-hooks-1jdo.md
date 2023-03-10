# 使用 React 挂钩跟踪状态使用情况，轻松实现渲染优化

> 原文：<https://dev.to/dai_shi/effortless-render-optimization-with-state-usage-tracking-with-react-hooks-1jdo>

##### 尝试 react-tracked 和 react-react-redux

### 简介

React useContext 非常便于避免正确钻孔。它可用于定义树中多个组件可以访问的全局状态或共享状态。

然而，useContext 不是专门为全局状态设计的，这里有一个警告。对上下文值的任何更改都会传播所有 useContext 以重新呈现组件。

这篇文章展示了一些关于状态使用跟踪的问题和解决方案的示例代码。

### 问题

让我们假设一个人对象是一个状态。

```
const initialState = {
  firstName: 'Harry',
  familyName: 'Potter',
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们使用上下文和本地状态。

```
const PersonContext = createContext(null);

const PersonProvider = ({ children }) => {
  const [person, setPerson] = useState(initialState);
  return (
    <PersonContext.Provider value={[person, setPerson]}>
      {children}
    </PersonContext.Provider>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，这里有一个组件显示这个人的名字。

```
const DisplayFirstName = () => {
  const [person] = useContext(PersonContext);
  return (
    <div>First Name: {person.firstName}</div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，一切顺利。然而，问题是当你更新这个人的姓而保持名字不变时。它会触发`DisplayFirstName`重新渲染，即使渲染结果是一样的。

请注意，这不是一个真正的问题，直到它成为一个问题。通常情况下，大多数较小的应用程序都可以工作，但一些较大的应用程序可能会有性能问题。

### 解决方案:状态使用追踪

让我们看看状态使用跟踪是如何解决这个问题的。

提供者看起来有点不同，但本质上是相同的。

```
const usePerson = () => useState(initialState);
const { Provider, useTracked } = createContainer(usePerson);

const PersonProvider = ({ children }) => (
  <Provider>
    {children}
  </Provider>
); 
```

Enter fullscreen mode Exit fullscreen mode

`DisplayFirstName`组件会这样改。

```
const DisplayFirstName = () => {
  const [person] = useTracked();
  return (
    <div>First Name: {person.firstName}</div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意到变化了吗？只是不同的是`useTracked()`而不是`useContext(...)`。

通过这个小小的改变，可以跟踪`DisplayFirstName`中的状态使用。而且现在即使姓氏更新了，只要名字没有更新，这个组件也不会重新呈现。

这是毫不费力的渲染优化。

### 高级例子

一些读者可能认为这也可以通过类似于`useSelector`的钩子来实现。

这里有另一个例子，其中`useTracked`要容易得多。

```
const initialState = {
  firstName: 'Harry',
  familyName: 'Potter',
  showFullName: false,
}; 
```

Enter fullscreen mode Exit fullscreen mode

假设我们有一个类似上面的状态，让我们创建一个带有条件的组件。

```
const DisplayPersonName = () => {
  const [person] = useTracked();
  return (
    <div>
      {person.showFullName ? (
        <span>
          Full Name: {person.firstName}
          <Divider />
          {person.familyName}
        </span>
      ) : (
        <span>First Name: {person.firstName}</span>
      )}
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

该组件将在两种情况下重新呈现。

*   a)当`firstName`或`familyName`更新时，如果显示全名
*   b)当`firstName`更新时，如果没有显示全名

用`useSelector`重现同样的行为并不容易，很可能以分离组件而告终。

### 项目使用状态使用情况跟踪

有两个项目使用状态使用跟踪。

#### 反应-反应-还原

[https://github.com/dai-shi/reactive-react-redux](https://github.com/dai-shi/reactive-react-redux)

这是 react-redux 的替代库。它有相同的钩子 API 和`useTrackedState`钩子。

#### 反应-跟踪

[https://github.com/dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)

这是一个没有 Redux 依赖的库。本帖中的例子就是基于此。它有一个与 reactive-react-redux 兼容的 hooks API。

## 期末备注

这篇文章关注的是如何轻松使用状态使用跟踪。我们没有讨论这些库的实现。

从技术上讲，有两个障碍。简而言之，我们使用代理 API 来跟踪状态使用情况。我们还在上下文 API 中使用一个未记录的特性来停止传播。如果你对这些内容感兴趣，请查看那些 GitHub 库。

* * *

最初发表于 2019 年 7 月 21 日[https://blog.axlight.com](https://blog.axlight.com/posts/effortless-render-optimization-with-state-usage-tracking-with-react-hooks/)。