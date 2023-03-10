# 原因行动中解释的反应上下文

> 原文：<https://dev.to/margaretkrutikova/reason-react-context-explained-in-action-5eki>

`react`中的 [`Context`](https://reactjs.org/docs/context.html) 是为位于组件树不同层次的组件之间共享一些全局数据而设计的。它允许避免将`props`一直传递到那些组件(“钻柱”)，同时在`context`中的值改变时仍然更新它们。

值得注意的是，建议使用`context`进行低频率更新(Sebastian markb ge 引用的[)，因为`react`查找上下文值订阅者的方式可能会影响性能。这个主题需要自己的文章(或者一本书？)，我在这里不会涉及它，而是集中在一个实际的例子上，在一个带有`ReasonML`的`react`应用程序中使用`context`进行很少的更新。](https://github.com/facebook/react/issues/14110#issuecomment-448074060)

## 我们在建造什么

我们将建立一个登录/注销功能，我们将把用户的信息放在`context`中，这样我们就可以从应用程序中的任何地方访问它，并根据用户是否匿名浏览来定制它。文章中的源代码在[这个回购](https://github.com/MargaretKrutikova/practical-reason-react/tree/master/context)里，有这个功能的迷你 app 的链接在[这里](https://practical-reason-react.netlify.com/context/)。

为了获得 react context 提供的好处和所有便利，有许多零碎的东西必须连接在一起，特别是在带有`ReasonML`的强类型环境中，但这绝对是值得的。

我将介绍将所有内容连接在一起所需的步骤，最后我们将使用一个简单的钩子，它允许从上下文中读取用户数据，并从任何组件调度和操作来更新它，就像这样:

```
let (user, dispatch) = UserContext.useUser();
let handleLogIn = () => dispatch(UserLoggedIn(userName));

switch (user) {
  | Anonymous => /** display login form */
  | LoggedIn(userName) => /** say hi to the user! */
}; 
```

Enter fullscreen mode Exit fullscreen mode

向下滚动以了解如何操作👇

## 创建提供者和上下文

我们将从这些步骤开始:

1.  创建上下文，
2.  创建提供程序组件，
3.  创建可重用的挂钩来访问上下文值。

我们需要知道使用我们 app 的用户是匿名的还是登录的，有哪些动作可以改变这一点，所以先说几个类型:

```
/** Types.re */
type user =
  | Anonymous
  | LoggedIn(string);

type userAction =
  | UserLoggedIn(string)
  | UserLoggedOut; 
```

Enter fullscreen mode Exit fullscreen mode

`LoggedIn`将保存用户名，但可以是具有更多用户数据的任何其他类型。当我们为用户状态实现一个缩减器时，我们将使用`userAction`。

现在让我们在文件`UserContext.re` :
中创建上下文和可重用的钩子来访问上下文值

```
/** initial value is Anonymous */
let context = React.createContext(Anonymous);

/** hook to easily access context value */
let useUser = () => React.useContext(context); 
```

Enter fullscreen mode Exit fullscreen mode

这与您在 JS 中的做法非常相似。现在让我们在文件`UserProvider.re`
中创建上下文提供者

```
/** UserProvider.re */
let make = React.Context.provider(UserContext.context);

/** Tell bucklescript how to translate props into JS */
let makeProps = (~value, ~children, ()) => {
  "value": value,
  "children": children,
}; 
```

Enter fullscreen mode Exit fullscreen mode

那个`makeProps`是干什么的，为什么我们不能用`[@react.component]`和`make`创建一个普通的组件呢？这个问题我问了自己很多次，直到我厌倦了，钻研了，才发现🤦‍♀️🙃

还记得我们如何在`reason`组件中为`props`命名参数，比如`~id`或`~className`吗？JS 没有这样的特性，所有正规的 JS 组件都只想拥有`props`作为对象。那么它如何编译成 JS 中有效的`react`组件呢？

这就是属性 [`[@react.component]`](https://reasonml.github.io/reason-react/docs/en/components#reactcomponent) 的用途。它将生成一个名为`makeProps`的函数，将这些命名的参数转换成一个 JS 对象，作为 JS 编译组件中的`props`。

`React.Context.provider`已经生成了一个 react 组件，它使用`props`作为 JS 对象，但是我们想使用它作为带有命名参数的`reason`组件。这就是我们手工创建`makeProps`的原因，它会告诉 bucklescript 如何将我们的命名参数转换成 JS 对象，由 JS 组件作为`props`使用。为了创建一个可以干净地编译成 JS 对象的对象，我们使用了 bucklescript [`Object 2`](https://bucklescript.github.io/docs/en/object-2) 绑定，如下所示:

```
{
  "value": value,
  "children": children,
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们基本上是在做`[@react.component]`的工作，但幸运的是这并不多，因为提供者只需要一个值和子元素😅。

我们现在可以使用像`<UserProvider...>`这样的提供者组件，因为我们遵循了在文件`UserProvider`中有两个函数`make`和`makeProps`的 [`convention`](https://reasonml.github.io/reason-react/docs/en/components#component-naming) 。

## 更新上下文中的值

现在，我们想使用我们的`Provider`组件并给它用户信息，当用户登录或注销时我们可以更新它。

这里要理解的重要的一点是，如果我们想要**更新**在`context`和**中的值，传播**对订户组件的更新，该值需要在某个组件的状态上。该组件需要向提供者组件呈现其自身状态的值。

姑且称之为`Root`组件:

```
/** Root.re */
type state = {user};

/** user and userAction defined in Types.re */
let reducer = (_, action) =>
  switch (action) {
  | UserLoggedIn(userName) => {user: LoggedIn(userName)}
  | UserLoggedOut => {user: Anonymous}
  };

[@react.component]
let make = () => {
  let (state, dispatch) = React.useReducer(reducer, {user: Anonymous});

  <UserProvider value=state.user>
    <Page />
  </UserProvider>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

酷，现在每当上下文中的值改变时，使用`useUser`的组件将被更新为新值！等等，这个值实际上永远不会改变..哦不！😯

让我们给组件提供通过上下文更新用户数据的可能性。我们可以将更新函数作为`props`向下传递，这将回到钻柱方法，但是更有趣的方法是将`dispatch`包含在上下文值本身中。

## 在上下文中传递分派

让我们将我们的`dispatch`和`user`一起作为上下文值传递。知道了`dispatch`接受`userAction`并返回`unit`，我们就可以在`UserContext.re` :
中修改上下文值的类型

```
/** UserContext.re */
type dispatch = userAction => unit;
type contextValue = (user, dispatch);

let initValue: contextValue = (Anonymous, _ => ignore());
/** no changes when creating context */ 
```

Enter fullscreen mode Exit fullscreen mode

和根组件:

```
/** Root.re */
let make = () => {
  let (state, dispatch) = React.useReducer(reducer, {user: Anonymous});

  <UserProvider value=(state.user, dispatch)>
    <Page />
  </UserProvider>;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 通过钩子使用上下文值

而现在我一开始承诺的奖励，一个好用方便的挂钩。我在这里再重复一遍，因为它很酷:

```
let (user, dispatch) = UserContext.useUser();
let handleLogIn = () => dispatch(UserLoggedIn(userName));

switch (user) {
  | Anonymous => /** display login form */
  | LoggedIn(userName) => /** say hi to the user! */
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 加成:优化技术

更新上下文值将导致订阅的组件重新呈现。在某些情况下，如果我们知道它们不会给我们的 UI 带来任何更新，我们可能会希望避免额外的重新渲染。例如，如果一个组件只需要通过`dispatch`更新用户，它不会对实际用户数据的任何更新感兴趣，但如果用户被更新，它仍然会重新呈现。

这可以通过在单独的上下文中使用`dispatch`函数来解决，该上下文不会更新，因为`dispatch`保证是稳定的。另一个上下文将拥有用户数据，并将更新依赖于它的组件。

当`Root`组件本身更新时(例如，如果它的`props`被更新)，它将重新创建上下文中传递的元组`(user, dispatch)`，并导致订阅的组件更新。这可以通过在上下文值周围使用`useMemo`使其稳定来解决。

* * *

我们现在已经设置了在我们的`reason-react`应用程序中存储和更新少量全局数据所需的所有上下文。我们还研究了`context`如何在`react`中工作以及组件如何在`reason-react`中编译的一些底层机制。

我错过了什么或者犯了什么错误吗？请在评论中让我知道。或者干脆写一行关于你如何在你的应用程序中使用`context`！💬