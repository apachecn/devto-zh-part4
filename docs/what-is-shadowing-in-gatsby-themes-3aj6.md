# 盖茨比主题中的阴影是什么？

> 原文：<https://dev.to/hagnerd/what-is-shadowing-in-gatsby-themes-3aj6>

这是盖茨比主题系列的延续。在以前的文章中，我们介绍了如何从头开始创建一个 Gatsby 网站，如何添加主题，以及如何使用主题选项来定制我们的网站。

我们将在《盖茨比》中学习阴影技术，这是一个强大的概念，它允许你使用主题，并以一种可靠的、符合人体工程学的方式定制你想要的部分。

* * *

## 什么是蔽？

《影子》是盖茨比的答案，它定制了主题的一部分，而没有把婴儿扔出洗澡水。如果你想改变一些样式、组件或内容，需要从头开始重新布线，那就糟了。

拥有一个巨大的对象，并为你希望用户能够覆盖的所有东西提供主题选项，这也很糟糕。如果某个主题创建者制作了一些不可覆盖的组件或样式，那也很糟糕。

通过使跟踪成为盖茨比工作方式的固有部分，就有了一种一致而简单的方法来覆盖几乎任何东西。

## 你能影子什么？

使用 Gatsby 主题，您可以隐藏样式、组件和数据需求(GraphQL 查询)。任何存在于主题目录中的东西都是公平的游戏！

## 你怎么影子？

让我们快速改变一些小的东西，比如我们博客的主色。我喜欢紫色，但你知道什么颜色更好吗？番茄。

为了从主题中隐藏一些东西，我们遵循一个简单的惯例。在我们的`src`文件夹中，我们创建了一个主题名为`gatsby-theme-blog`的文件夹，然后我们模仿主题中文件的路径，但是我们忽略了主题中的`src`。

于是`gatsby-theme-blog/src/gatsby-plugin-theme-ui/colors.js`变成了`src/gatsby-theme-blog/gatsby-plugin-theme-ui/colors.js`。

因为我们碰巧想改变颜色，而且我碰巧知道`gatsby-theme-blog/src/gatsby-plugin-theme-ui/colors.js`正是我们需要阴影的文件，所以我们需要创建一些文件夹，并在我们的 Gatsby 网站中创建一个新文件，这样我们就可以对它进行阴影处理。

```
mkdir -p src/gatsby-theme-blog/gatsby-plugin-theme-ui
touch src/gatsby-theme-blog/gatsby-plugin-theme-ui/colors.js 
```

现在打开你最喜欢的代码编辑器，我们将打开两个文件。文件 1) `node_modules/gatsby-theme-blog/src/gatsby-plugin-theme-ui/colors.js`和文件 2) `src/gatsby-theme-blog/gatsby-plugin-theme-ui/colors.js`。

当我阴影化的时候，我发现在处理新文件的时候，看着我要阴影化的东西是最容易的。

您的`node_modules/gatsby-theme-blog/src/gatsby-plugin-theme-ui/colors.js`文件应该是这样的:

```
const purple60 = `#663399`
const purple30 = `#D9BAE8`
const grey90 = `#232129`
const black80 = `#1B1F23`
const white = `#fff`
const lightWhite = `rgba(255, 255, 255, 0.86)`
const opaqueLightYellow = `rgba(255, 229, 100, 0.2)`
const opaqueLightWhite = `hsla(0, 0%, 100%, 0.2)`
const lightGray = `hsla(0, 0%, 0%, 0.2)`

export default {
  text: grey90,
  background: white,
  primary: purple60,
  secondary: black80,
  muted: lightGray,
  highlight: opaqueLightYellow,
  heading: grey90,
  prism: {
    background: `#011627`,
    comment: `#809393`,
    string: `#addb67`,
    var: `#d6deeb`,
    number: `#f78c6c`,
    constant: `#82aaff`,
    punctuation: `#c792ea`,
    className: `#ffc98b`,
    tag: `#ffa7c4`,
    boolean: `#ff5874`,
    property: `#80cbc4`,
    namespace: `#b2ccd6`,
    highlight: `hsla(207, 95%, 15%, 1)`,
  },
  modes: {
    dark: {
      text: lightWhite,
      background: grey90,
      primary: purple30,
      secondary: lightWhite,
      muted: opaqueLightWhite,
      highlight: purple60,
      heading: white,
    },
  },
} 
```

我们想要改变的颜色是对象中的`primary`键。

```
export default {
  text: gray90,
  background: white,
  primary: purple60,
  //...
} 
```

那么我们能从这个文件中学到什么呢？首先要做的是，我们应该在影子`colors.js`文件中有一个默认的导出。第二，除非我们想改变一堆这些值，否则我们需要一些方法来合并`gatsby-theme-blog`颜色和我们的颜色，并覆盖我们想要的颜色。

在我们弄清楚如何合并我们的颜色对象之前，让我们设置一个导出默认值，并将主键设置为“tomato”。

```
export default {
  primary: 'tomato',
} 
```

现在最酷的部分...

```
import colors from 'gatsby-theme-blog/src/gatsby-plugin-theme-ui/colors'

export default {
  ...colors,
  primary: 'tomato',
  modes: {
    dark: {
      ...colors.modes.dark,
      primary: '#ff755e'
    }
  }
} 
```

我们可以导入我们正在隐藏的东西，使用我们想要合并的任何 JavaScript 方法，并覆盖我们想要的任何东西。就这么简单。至少更新样式就是这么简单。

## 为什么隐藏是这样工作的？

当你使用一个主题时，Gatsby 首先尝试在你自己的项目`src`文件夹中解析，在`src/{theme-name}/{any-sub-dirs}/{file}`中查找，如果它在那里没有看到任何东西，它就解析到`node_modules/{theme-name}/src/{any-sub-dirs}/{file}`。

当建立一个主题时，你不需要做任何特殊的事情来允许阴影。它只是工作。当你使用一个主题时，你可以遵循这些惯例来隐藏你需要的任何东西。

## 奖金

想要一种更简单的方法来对 JavaScript 中的对象进行部分/深度更新吗？有一个非常酷的库叫做 [immer](https://github.com/immerjs/immer) ，它在这方面特别擅长。

只需将 immer 安装到项目中，将其导入到我们的文件中，然后让奇迹发生。

```
import produce from 'immer'
import colors from 'gatsby-theme-blog/src/gatsby-plugin-theme-ui/colors'

export default produce(colors, draft => {
  draft.primary = 'tomato'
  draft.modes.dark.primary = '#ff755e'
}) 
```

* * *

## 结论

阴影是 Gatsby 中的一个概念，它允许我们覆盖和组合来自 Gatsby 主题的样式、组件和数据。它遵循一个简单的路径解析惯例来允许阴影。主题的`src/`文件夹中的任何东西都可以被隐藏。

### 接下来

下一次，我们将深入到阴影`gatsby-theme-blog`中，通过阴影样式和组件使它成为我们自己的。