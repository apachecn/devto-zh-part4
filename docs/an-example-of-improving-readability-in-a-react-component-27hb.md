# 提高 React 组件可读性的示例

> 原文：<https://dev.to/wolverineks/an-example-of-improving-readability-in-a-react-component-27hb>

最近，有人要求对他们编写的组件进行第二次检查。他们表示，他们想要一种更好的方式来编写它，但没有具体说明他们认为可以改进的地方。

在这种情况下，我喜欢吟唱咒语

> 让它工作
> 让它漂亮
> 让它快

在这种情况下，代码是有效的。所以，继续第二步。(我很少进行第三步)

下面是原始代码。

```
// yada yada yada, imports...

interface StatusBarProps {
  accent: string;
}

const MyStatusBar = ({ accent }: StatusBarProps) => {
  switch (accent) {
    case "yellow":
      if (Platform.OS === "ios") {
        return (
          <View
            style={{
              width: "100%",
              height: getStatusBarHeight(),
              backgroundColor: COLORS.yellow,
            }}
          >
            <StatusBar
              translucent={false}
              barStyle="light-content"
              backgroundColor={COLORS.yellow}
            />
          </View>
        );
      } else {
        return (
          <StatusBar
            translucent={false}
            barStyle="light-content"
            backgroundColor={COLORS.yellow}
          />
        );
      }
    case "purple":
      if (Platform.OS === "ios") {
        return (
          <View
            style={{
              width: "100%",
              height: getStatusBarHeight(),
              backgroundColor: COLORS.purple,
            }}
          >
            <StatusBar
              translucent={false}
              barStyle="light-content"
              backgroundColor={COLORS.purple}
            />
          </View>
        );
      } else {
        return (
          <StatusBar
            translucent={false}
            barStyle="light-content"
            backgroundColor={COLORS.purple}
          />
        );
      }
    default:
      return (
        <StatusBar
          translucent={false}
          barStyle="light-content"
          backgroundColor={COLORS.white}
        />
      );
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 它有什么作用？

我的第一个观察结果是，这个组件不会发出什么样的声音。一个真正的*10 倍开发人员读的代码比他们写的多 10 倍。因此，你所做的任何减少别人阅读你的代码的时间的事情都会有回报。*

这个组件似乎做了两件事:

*   在 ios 而不是 android 上添加一个`<View>`包装器
*   基于道具设置背景颜色

嗯， [unix 哲学](https://en.wikipedia.org/wiki/Unix_philosophy)，粗略地说，就是

> 做一件事，并把它做好。

所以，让我们把它分成几部分。

### 平台特定进口

在 React 原生文档中，您可以找到关于[平台特定导入](https://facebook.github.io/react-native/docs/platform-specific-code#platform-specific-extensions)的部分

这意味着在使用`import { MyStatusBar } from "./MyStatusBar`时，react-native 会自动从 android 上的`MyStatusBar.android.tsx`和 ios 上的`MyStatusBar.ios.tsx`导入组件。这样，我们可以从组件中移除基于平台的逻辑。

```
if (Platform.OS === "ios") {
  ...yada yada ios stuff
} else {
  ...yada yada android stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

### 一个`switch`语句做什么？

在 switch 语句中，情况似乎非常相似。这就很难知道`switch`语句在做什么。要确定一个特定的`switch`语句在做什么，看看`case`之间有什么不同。在这个例子中，`case`之间唯一的不同是传递给`backgroundColor`的值。因此，如果我们将这个`switch`语句限制为仅确定该值，我们可以将组件简化为:

```
// MyStatusBar.android.tsx
// ...stuff

let backgroundColor = COLORS.white
switch (accent) {
  case "yellow": backgroundColor = COLORS.yellow
  case "purple": backgroundColor = COLORS.purple
}

return <StatusBar translucent={false}
  barStyle="light-content"
  backgroundColor={backgroundColor} /> 
```

Enter fullscreen mode Exit fullscreen mode

或者更好的是

```
// MyStatusBar.android.tsx
// ...stuff

const DEFAULT_BACKGROUND_COLOR = COLORS.white

// ...more stuff

return <StatusBar translucent={false}
  barStyle="light-content"
  backgroundColor={COLORS[accent] || DEFAULT_BACKGROUND_COLOR} /> 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

当编写一个组件或任何相关的代码时，我会问自己一些关于第一次或一段时间以来第一次阅读这些代码的未来开发人员的问题...

*   他们需要多长时间才能知道这个代码是干什么的？
*   代码中有哪些部分需要他们比其他人花更长的时间才能理解？
*   他们需要知道这些代码的每一部分是做什么的吗，或者其中的一些可以抽象出来吗？
*   他们知道比这里使用的任何模式更标准化/更容易识别的替代模式吗？
*   他们是否对任何变量的命名感到困惑？
*   他们对参数的数量或类型感到困惑吗？
*   为了在将来实现一些可能的更改，他们需要更改代码的哪些部分？
*   当实现变更时，代码的哪一部分更有可能成为致命一击？

所以是的，这就是那个话题。

我对你重构 React 组件以增加可读性的例子很感兴趣，所以请把它们放在评论里。

谢了。