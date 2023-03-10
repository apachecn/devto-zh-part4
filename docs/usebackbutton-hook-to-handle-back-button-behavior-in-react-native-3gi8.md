# “使用后退按钮”挂钩处理 React Native 中的后退按钮行为

> 原文：<https://dev.to/farskid/usebackbutton-hook-to-handle-back-button-behavior-in-react-native-3gi8>

[React hooks](https://reactjs.org/docs/hooks-intro.html) 有助于携带有状态逻辑，并使它们的生命周期与视图层生命周期分开。自从 React v16.8 以来它们就存在了，因为人们一直在避免类组件而支持钩子。

钩子的一个有趣的方面是它们在组成和抽象方面的灵活性。如果使用不同[内置钩子](https://reactjs.org/docs/hooks-reference.html)的代码片段正在处理与其他部分分离且独立的逻辑部分，那么它可以被抽象为一个 [**自定义钩子**](https://reactjs.org/docs/hooks-custom.html) 。

## 使用后退按钮自定义挂钩

为了使用 React Native 处理 Android 和 tvOS 设备中硬件后退按钮的行为，有 [*BackHandler*](https://facebook.github.io/react-native/docs/backhandler.html) API 可以帮助覆盖默认行为或修补它。

采用事件驱动的方法来提供 API，这意味着要订阅后退按钮，您需要注册一个事件监听器。

```
import { BackHandler } from "react-native";

function backButtonHandler() {}

BackHandler.addEventListener("hardwareBackPress", backButtonHandler); 
```

Enter fullscreen mode Exit fullscreen mode

当然，要取消注册这个订阅，您需要使用`removeListener`，就像我们处理 DOM 事件一样。

```
backButtonHandler.removeEventListener("hardwareBackPress", backButtonHandler); 
```

Enter fullscreen mode Exit fullscreen mode

### 使用钩子来利用 BackHandler

订阅是副作用！因此，我们可以使用`useEffect`内置钩子来执行它们。需要注意的是`useEffect`要求我们返回一个函数，一旦 React 愿意清理，就删除订阅！当组件被卸载或者`useEffect`钩子的一个依赖项改变时，通常需要清理，所以 React 需要清理钩子以前的订阅并重新执行它！

[在*使用效果*](https://reactjs.org/docs/hooks-effect.html#effects-with-cleanup)
阅读更多关于订阅和清理的信息

```
function backButtonHandler() {}

function MyComponent() {
  useEffect(() => {
    BackHandler.addEventListener("hardwareBackPress", backButtonHandler);

    return () => {
      BackHandler.removeEventListener("hardwareBackPress", backButtonHandler);
    };
  }, [backButtonHandler]);

  return <View>...</View>;
} 
```

Enter fullscreen mode Exit fullscreen mode

### useBackButton 诞生！

虽然上面的代码片段运行得很完美，但是将它复制粘贴到所有的屏幕上可能会让我们中的一些人感到沮丧(毕竟我们是怪人)！为了帮助消除我们的挫折感，React 提供了一种方法来建立您的自定义挂钩。

```
/* ComponentA */
function backButtonHandlerForA() {}
function ComponentA() {
  // Frustration begins!
  useEffect(() => {
    BackHandler.addEventListener("hardwareBackPress", backButtonHandlerForA);

    return () => {
      BackHandler.removeEventListener(
        "hardwareBackPress",
        backButtonHandlerForA
      );
    };
  }, [backButtonHandlerForA]);

  return <ViewA />;
}

/* ComponentB */
function backButtonHandlerForB() {}
function ComponentB() {
  // Frustration begins!
  useEffect(() => {
    BackHandler.addEventListener("hardwareBackPress", backButtonHandlerForB);

    return () => {
      BackHandler.removeEventListener(
        "hardwareBackPress",
        backButtonHandlerForB
      );
    };
  }, [backButtonHandlerForB]);

  return <ViewB />;
} 
```

Enter fullscreen mode Exit fullscreen mode

自定义钩子只是在组件和钩子之间共享相同逻辑的抽象，就像我们在常规编程中在函数之间做的一样。
组件是功能。钩子也是函数。你明白了，对吧？！

在上述*组件 A* 和*组件 B* 样本的情况下，注册、移除和挂钩实现是相同的。只是每个组件的处理程序可能不同。所以我们的定制钩子需要提供那些公共部分，并且只接受变化的部分(每个组件的处理程序)作为传入参数。

```
/* useBackButton */
function useBackButton(handler) {
  // Frustration isolated! Yay! 🎉
  useEffect(() => {
    BackHandler.addEventListener("hardwareBackPress", handler);

    return () => {
      BackHandler.removeEventListener(
        "hardwareBackPress",
        handler
      );
    };
  /* now that the handler is an external dependency, once handler 
    changes, we wanna be able to reflect that change and update the 
    handler inside our hook too. so we need to pass handler as a 
    dependency to our custom hook
  */
  }, [handler]);
}

/* ComponentA */
function backButtonHandlerForA() {}
function ComponentA() {
  // utilize custom hook
  useBackButtton(backButtonHandlerForA);
  return <ViewA />;
}

/* ComponentB */
function backButtonHandlerForB() {}
function ComponentB() {
  // utilize custom hook
  useBackButtton(backButtonHandlerForB);

  return <ViewB />;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重述

如果您打算在组件和内置挂钩之间共享一个公共逻辑，您可以通过构建您的自定义挂钩来将其抽象出来。请务必阅读令人印象深刻的详细文件的钩子和**规则的钩子**从官方文件。

干杯！