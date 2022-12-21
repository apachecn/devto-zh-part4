# react navigation reason ml 中的自动流演示

> 原文：<https://dev.to/idkjs/reactnavigation-authflow-demo-in-reasonml-3klm>

[![Alt Text](img/849bbb1ca63aa500f9da0ee7662b4d31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E-oY_gmf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1tsbu6xyjzqjhlkbf7ym.png)

## 动机

在 ReasonML 中复制`react-navigation` AuthFlow 演示。演示在这里:[https://reactnavigation.org/docs/en/auth-flow.html](https://reactnavigation.org/docs/en/auth-flow.html)。我需要了解如何让一些基本的`switch`和`stack`导航在`ReasonML`中协同工作。导航的绑定是`WIP`在[优秀](https://github.com/reasonml-community/reason-react-native)[原因-反应-原生](https://github.com/reasonml-community/reason-react-native)回购。更广泛地说，在运行中学习这个需要时间，为了缩小我必须学习的范围，我决定尝试限制自己使用 react-native/react-native-web，当然是在#ReasonML 中，为了所有 ui 的发展。这意味着我必须为任何移动应用版本设计一些基本的路线。

这个版本有点粗糙。屏幕过渡似乎不流畅。我相信我最终会明白的。我刚刚开始学习反应导航，所以我想我会很快学到这方面的知识。这里的代码是[这里的](https://github.com/idkjs/react-navigation-authflow-in-reasonml)。

## 外卖

这个错误对我来说是新的:

```
Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in %s.%s, a useEffect cleanup function, 
```

我一开始并没有真正理解这一点，因为应用程序正在运行，但却抛出了这个警告。经过研究和询问，我了解到在检查用户的 auth 状态后，我需要`clean up`我的`React.useEffect`函数来卸载。

那是在 [`src/AuthLoadingScreen.re`](https://github.com/idkjs/react-navigation-authflow-in-reasonml/blob/master/src/AuthLoadingScreen.re) 在
完成的

```
 React.useEffect1(
    () => {
      Utils.checkAuthWithRoute(~navigation) |> ignore
      // cleanup function that does nothing
      // thanks to @thangngoc89 for the tip on cleaning up useEffect
      Some(() => ());
    },
    [||],
  ); 
```

最重要的是完成它。只有在用户登录后刷新 IOS 模拟器时，我才继续遇到这个问题。因此，由于它在没有用户登录的情况下工作正常，所以我尝试复制那个设置。我最终将 routing sign out 函数发送回了`AuthLoadingScreen.re`模块，并让该组件决定下一步去哪里。我不知道这是否是推荐的做事方式，但它适用于本演示的目的。 [`src/utils/Utils.re`](https://github.com/idkjs/react-navigation-authflow-in-reasonml/blob/master/src/utils/Utils.re) 发生的一切。这有一个意想不到的效果，清理了剩余的视图屏幕，因为我最终直接在 auth promises 中调用了大部分转换逻辑。

## 运行 app

```
git clone https://github.com/idkjs/react-navigation-authflow-in-reasonml.git
cd react-navigation-authflow-in-reasonml
yarn install 
```

在另一个目录下，运行`yarn start`启动`expo`。在终端中点击`i`打开模拟器中的应用程序。或者其他五种可能的方法。

有任何问题可以打 [@_idkjs](https://twitter.com/_idkjs) 找我。