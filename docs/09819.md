# 如何扩展 Tailwind CSS

> 原文：<https://dev.to/justalever/how-to-extend-tailwind-css-14d6>

Tailwind CSS 作为实用程序优先的 CSS 框架提供了有力的支持。即使有一些惊人的缺省值可供您使用，也有需要扩展框架的时候。在这篇文章中，学习如何进一步扩展 Tailwind CSS。

[https://www.youtube.com/embed/HVRnRp26_MQ](https://www.youtube.com/embed/HVRnRp26_MQ)

## 用例

下面的每个用例都是 *I* 会做的事情。它们绝不意味着你自己应该如何处理它。考虑一下我在使用框架的过程中学到的技巧和诀窍。

### 内部/个人方法

扩展 Tailwind 的第一个也可能是最明显的方法是使用 CSS 方法。这样做可能如下所示:

注意:在这个视频/例子中，我引用了 [Tailwind CSS Github 项目页面](https://github.com/tailwindcss)上的[游乐场项目](https://github.com/tailwindcss/playground)。这使用基本的依赖关系来帮助你直接进入。在本系列的前几期文章中，我将带您了解如何启动并运行它。找到这篇文章末尾的链接。

#### 通过 CSS 添加新的工具

新的实用程序类应该只出现在主样式表中的`@tailwind`指令之后。

```
/* tailwind.css*/
@tailwind base;
@tailwind components;
@tailwind utilities;

.rotate-0 {
  transform: rotate(0deg);
}
.rotate-90 {
  transform: rotate(90deg);
}
.rotate-180 {
  transform: rotate(180deg);
}
.rotate-270 {
  transform: rotate(270deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

这些类现在可以和你的顺风配置捆绑在一起工作。我应该注意到，我使用的是由[的顺风 CSS 文档](https://tailwindcss.com)提供的默认。

如果我们想让`rotate-`类也有响应能力呢？输入方便提供的`@responsive`指令。

```
/* tailwind.css*/
...
@responsive { 
  .rotate-0 {
    transform: rotate(0deg);
  }
  .rotate-90 {
    transform: rotate(90deg);
  }
  .rotate-180 {
    transform: rotate(180deg);
  }
  .rotate-270 {
    transform: rotate(270deg);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

悬停/聚焦状态呢？与`@responsive`相同的概念也适用于此。我们甚至可以把所有东西都放在`@responsive`中，如下所示:

```
/* tailwind.css*/
@responsive {
  @variants hover, focus {
    .rotate-0 {
      transform: rotate(0deg);
    }
    .rotate-90 {
      transform: rotate(90deg);
    }
    .rotate-180 {
      transform: rotate(180deg);
    }
    .rotate-270 {
      transform: rotate(270deg);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

相当酷！这个用例可能是内部项目中最有用的。这些可能是你自己或在团队环境中创建的项目。如果你使用 Sass 或者更少，你可以将这些额外的实用程序提取到一个部分中，并使用`@import`导入 CSS 以减少文件中的代码行数。

### 花式 PostCSS 方法

使用`@apply`语句，添加现有的顺风实用程序类是完全可能的。也许您想为表单定义一些组件，并利用 Tailwind。理想情况下，您希望从 HTML 标记中提取所有的类。可能看起来像这样。

```
.input {
  @apply appearance-none block w-full bg-white text-gray-700 border border-black rounded py-3 px-4 leading-none; 
}

.select {
  @apply appearance-none py-3 px-4 pr-8 block w-full bg-white border border-black text-gray-700
   rounded leading-tight;
  -webkit-appearance: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做的好处是将已经存在的 Tailwind 实用程序类分组，以便在单个限定了作用域的类名中重用。你的应用程序/网站中经常重复的元素是这种类型分组的最佳候选。

### 外部/开源方式

另一种扩展 Tailwind CSS 的方法涉及更多的 JavaScript 配置。虽然有点复杂，但是这种方法允许您提取逻辑供他人(或您)在其他地方使用。例如，您可以将它包装成一个节点模块，或者仅仅是一个为您或他人定义定制组件的模块库。天空是极限。

例如，添加按钮组件(无需在项目的样式表中编写任何额外的 CSS)可能如下所示:

```
// tailwind.config.js
module.exports = {
  // a bunch of other tailwind settings
  plugins: [
    function({ addComponents }) {
      const buttons = {
        '.btn': {
          padding: '.5rem 1rem',
          borderRadius: '.25rem',
          fontWeight: '600',
        },
        '.btn-primary': {
          backgroundColor: '#4299e1',
          color: '#fff',
          '&:hover': {
            backgroundColor: '#3182ce'
          },
        },
        '.btn-secondary': {
          backgroundColor: '#667eea',
          color: '#fff',
          '&:hover': {
            backgroundColor: '#5a67d8'
          },
        },
      }

      addComponents(buttons)
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`addComponents`功能是针对顺风配置的。我们将其析构，并能够传递 CSS-in-JS 的对象。我们相应地在`tailwind.config.js`文件的`plugins`数组中定义了这个函数。您也可以从项目中的另一个位置或者甚至从一个单独的节点模块中要求这样做。

```
// tailwind.config.js
module.exports = {
  plugins: [
    require('./plugins/buttons')
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里看到的主要缺点是无法访问直接 CSS 中使用的预先存在的实用程序类。您可以引用一个给定的用户配置，但这样做有点复杂。

除了`addComponents`函数之外，你还可以使用其他几个函数来挂钩和扩展 Tailwind。以下是完整的列表和每个列表的快速摘要:

*   `addUtilities()`，用于注册新的实用样式
*   `addComponents()`，用于注册新的组件样式
*   `addBase()`，用于注册新的基础款式
*   `addVariant()`，用于注册自定义变量
*   `e()`，用于转义打算在类名中使用的字符串
*   `prefix()`，用于手动将用户配置的前缀应用到选择器的各个部分
*   `theme()`，用于查找用户主题配置中的值
*   `variants()`，用于在用户的变型配置中查找值
*   `config()`，用于查找用户顺风配置中的值

我经常发现自己使用`addBase()`作为插件来扩展`@tailwind base`。我相应地将标题级别设置为特定的默认值，因为它们的大小和样式都是完全重置的。

```
// tailwind.config.js
module.exports = {
  plugins: [
    function({ addBase, config }) {
      addBase({
        'h1': { fontSize: config('theme.fontSize.3xl') },
        'h2': { fontSize: config('theme.fontSize.2xl') },
        'h3': { fontSize: config('theme.fontSize.xl') },
        'h4': { fontSize: config('theme.fontSize.lg') },
      })
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我建议好好阅读一下[插件文档](https://tailwindcss.com/docs/plugins/)。这里还有很多我没有涉及到的东西，你可以在自己的项目中很容易地配置和扩展 Tailwind CSS。

上面的许多代码示例和文档片段摘自亚当·瓦森构建和维护的[tailwindcss.com](https://tailwindcss.com)。去支持他！

### 到目前为止的级数

*   [如何安装顺风 CSS v1.0](https://dev.to/justalever/how-to-install-tailwind-css-v1-0-p42)
*   [让我们建造:顺风 CSS–Tweet](https://dev.to/justalever/let-s-build-with-tailwind-css-tweet-3ejh)
*   [让我们建立:顺风 CSS-Dribbble Shot](https://web-crunch.com/lets-build-tailwind-css-dribbble-shot)
*   [让我们构建:使用顺风 CSS 响应式导航条](https://web-crunch.com/lets-build-tailwind-css-responsive-navbar)
*   [让我们构建:顺风 CSS 电影制作登录页面](https://dev.to/justalever/let-s-build-with-tailwind-css-movie-production-landing-page-6o1)

帖子[如何扩展 Tailwind CSS](https://web-crunch.com/how-to-extend-tailwind-css/) 最早出现在 [Web-Crunch](https://web-crunch.com) 上。