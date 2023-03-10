# 最好的反应最好的本地 UI 库

> 原文：<https://dev.to/salted-bytes/best-of-the-best-react-native-ui-libraries-31p3>

在这篇文章中，我将和你讨论我认为最好的 React 原生用户界面库。

上面的句子中有一个重要的要点。这是我的看法。如果你不同意也没关系。

这些年来，我已经尝试了大量的 React 原生用户界面库，但总体来说，我的印象并不深刻。这并不是说它们不好。远非如此！它们要么没有满足我的需求，要么过于复杂，难以覆盖/实现。

我以前试过的一些，建议你也试试:

*   [原生基地](https://nativebase.io)
*   [反应原生元素](https://react-native-training.github.io/react-native-elements/)
*   [UI 小猫](https://akveo.github.io/react-native-ui-kitten/)

在列出的这些中，React Native Elements 是我目前最喜欢的。直到我发现了 [React Native Paper](https://reactnativepaper.com/) 。

[React Native Paper](https://reactnativepaper.com/) 来自 [Callstack](https://callstack.com/) 的牛逼家伙，它遵循材料设计原则，跨平台产生真正一致的结果。

当然，材料设计并不适合每个人的口味，也不适合所有的情况，但是如果你需要一些完全不同的东西，那么从零开始，完全不用库，可能是值得的。话虽如此，如果你对材料设计很满意，或者你想快速完成一些东西，但不想在 UI 上花太多时间，那么这绝对是一个适合你的库。

以前，我只在需要快速创建东西时才真正使用库，但是 React Native Paper 非常灵活，很容易被覆盖，我可以看到自己在更大的项目中也使用它。事实上，我目前正在使用的是一个应用程序，我希望在 11 月下旬发布到应用程序商店。

## 我为什么爱 React 原生纸:

### 易于设置

React Native Paper 文档非常棒！如同[设置指令](https://callstack.github.io/react-native-paper/getting-started.html)一样。

你需要做的就是:

*   安装它:

```
yarn add react-native-paper

# if in a vanilla RN app you need to add icons
yarn add react-native-vector-icons
react-native link react-native-vector-icons 
```

*   将其添加到您的项目中:

```
import * as React from "react";
import { AppRegistry } from "react-native";
import { Provider as PaperProvider } from "react-native-paper";
import App from "./src/App";

export default function Main() {
  return (
    <PaperProvider>
      <App />
    </PaperProvider>
  );
}

AppRegistry.registerComponent("main", () => Main); 
```

这就是开始工作所需的全部内容。有一些可选的步骤(添加一个 babel 插件)可以采取，但上面的代码是你所需要的一切。

### 主题化

我们都希望我们的应用程序能够脱颖而出，所以我们希望能够轻松地修改 React Native Paper 的主题。这才是真正出彩的地方。他们使改变基本主题变得如此容易。你只需要覆盖你想要改变的主题部分，并将你的新主题传递给`PaperProvider`

```
import * as React from "react";
import { DefaultTheme, Provider as PaperProvider } from "react-native-paper";
import App from "./src/App";

const theme = {
  ...DefaultTheme,
  colors: {
    ...DefaultTheme.colors,
    primary: "tomato",
    accent: "yellow"
  }
};

export default function Main() {
  return (
    <PaperProvider theme={theme}>
      <App />
    </PaperProvider>
  );
} 
```

你想在你自己的定制组件中使用什么主题？React Native Paper 支持你。你只需将你的组件包在`withTheme` HOC 中，然后嘣！你可以进入主题。

```
import * as React from "react";
import { withTheme } from "react-native-paper";

function MyComponent(props) {
  const { colors } = props.theme;
  return <Text style={{ color: colors.primary }}>Yo!</Text>; }

export default withTheme(MyComponent); 
```

主题化有更多的选择，所以查看一下[文档](https://callstack.github.io/react-native-paper/theming.html)

## 组件

React Native Paper 提供了构建下一个项目所需的几乎所有组件。他们还推荐了几个与该库配合良好的组件。在[文档网站](https://callstack.github.io/react-native-paper/)上可以找到包含示例的完整组件列表。

正如我之前提到的，组件的 API 很容易理解，所以这个库很容易使用。

### 文档

这个库的文档很棒。它简洁明了。所有的组件都有[世博小吃](https://snack.expo.io)，所以你可以很容易地看到它们的动作。

## 总之

### 反应原生纸:

[![Bill Murray](img/f5e9d0eaa3215caa9fb55090d2aadf97.png)](https://i.giphy.com/media/fem4oy4LOfbL4G64ie/giphy.gif)