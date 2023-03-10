# 如何用 Gatsby & React Hooks 实现“黑暗模式”

> 原文：<https://dev.to/kim_hart/how-to-implement-dark-mode-with-gatsby-react-hooks-k5a>

我最近推出了我的新投资组合网站，我不得不说，我非常自豪！在我学会如何编码之前，我的旧网站是在 Wix 上建立的，需要一个重大的设计更新。

我登陆了 [Gatsby](https://www.gatsbyjs.org/) 作为我的设置和 [Netlify](https://www.netlify.com/) 作为我的部署平台和伙计们，我不能推荐他们每一个。但是这篇文章不是关于那个的！

## 为什么是黑暗模式？

事实证明，很多人喜欢黑暗主题的网络事物(问问 Twitter 就知道了)。我选择在我的作品集上实现**可切换的日出和日落主题**,因为它为我的静态网站增加了一个互动层次，允许我玩更复杂的 CSS，并且它让用户定制他们的体验。它甚至通过`localStorage`在会话中持续存在！

## 我用了什么？

我考虑过自己做这个，直到我发现了这个叫做[使用黑暗模式](https://github.com/donavon/use-dark-mode)的工具。简而言之，它是一个自定义的 React 挂钩，为您处理存储部分。他们的文档非常棒，但我也会向您介绍我的使用案例。

## 实现

*   您必须使用包含挂钩的`react@16.8.0`或更高版本
*   这只在**功能组件**中起作用，所以如果你使用旧的 React 类组件和非钩子生命周期方法，你可能会有一段艰难的时间。

### 1。安装

你将安装`use-dark-mode`和它的 Gatsby 特定插件，该插件有助于你的主题的整体渲染:

```
yarn add use-dark-mode gatsby-plugin-use-dark-mode 
```

Enter fullscreen mode Exit fullscreen mode

### 2。添加到 Gatsby 配置

为了防止页面加载时默认样式的内容闪烁，将这个块添加到您的`gatsby-config.js`文件中。(更多尽在[文档](https://www.gatsbyjs.org/packages/gatsby-plugin-use-dark-mode/) )

```
{  resolve:  "gatsby-plugin-use-dark-mode",  options:  {  classNameDark:  "dark-mode",  classNameLight:  "light-mode",  storageKey:  "darkMode",  minify:  true,  },  } 
```

Enter fullscreen mode Exit fullscreen mode

注意:您可以随意命名这些类！我坚持使用默认设置。

### 3。添加以做出反应

这里是我的英雄组件的超简化版本。它包含两个图标组件(日出和日落),点击后触发`handleTheme`,根据它们的道具启动`darkMode.enable()`或`darkMode.disable()`。

这里的目标是当你点击日落时切换到黑暗模式，当你点击日出时切换到明亮模式。

```
 import React from "react"
import useDarkMode from "use-dark-mode"
import Sunrise from "../components/icons/sunrise"
import Sunset from "../components/icons/sunset"

const Hero = () => {
  // Instantiate with the default behavior, in this case, it defaults to light-mode
 // This places "light-mode" class on document.body, as outlined in my gatsby-config.js
  const darkMode = useDarkMode(false);

  // Custom function that handles the toggling
  // When called, it replaces the class on document.body and holds it in localStorage
  const handleTheme = theme => theme === "dark" ? darkMode.enable() : darkMode.disable();

  return (
    <div className="hero">
      <Sunrise onClick={handleTheme} />
      <Sunset onClick={handleTheme} />
    </div>
  )
}

export default Hero; 
```

Enter fullscreen mode Exit fullscreen mode

日落和日出图标组件超级相似，它们只是传递了不同的值(“亮”和“暗”)。这里有一个精简版的日落:

```
import React from "react"

const Sunset = (props) => {
  // If the `onClick` prop exists, call it with 'dark'
  const handleClick = () => props.onClick && props.onClick('dark');

  return (
    <div className="theme-toggle" onClick={handleClick}>...</div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

*   注意:您也可以通过传递布尔值来实现这一点(例如，“true”表示黑暗，但是我选择让它更具可读性并使用字符串)

## CSS

现在我们已经让`document.body`上的类在点击日出或日落图标时在亮模式和暗模式之间切换，我们可以调整我们的 CSS 来反映这些变化。

我用的更少，这使得基于父值应用规则变得超级容易。同样，这是简化的，但希望你能明白。

`.dark-mode &`选择器将寻找任何时候`dark-mode`类存在于更高的组件上(在本例中是`body`标签)。然后，您可以应用您需要的任何规则—在这种情况下，它是背景颜色的一个变量。

```
 .hero {
      background: @sunrise-gradient;
      .dark-mode & {
        background: @sunset-gradient;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

...就是这样！

## 结论

你不必完全重新创建轮子来实现盖茨比应用程序中的黑暗模式。希望这是有帮助的，我很乐意回答评论中的任何问题！