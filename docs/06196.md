# 自定义夜间模式切换 w/React & CSS 变量

> 原文：<https://dev.to/ninjasoards/make-a-custom-night-mode-toggle-w-react-css-variables-272o>

这篇文章是一个简短系列的第一篇，我将详细介绍我在设计和开发一个网站来推广 AIGA 路易斯维尔的年度设计周时学到的东西。请看看，并在评论中告诉我你的想法。

[louisvilledesignweek.com](https://louisvilledesignweek2019.netlify.app/)

偷看我的[源代码](https://github.com/davidysoards/louisville-design-week)👀

[第二部分:动画霓虹灯 SVG](https://dev.to/ninjasoards/make-a-flickering-neon-svg-animation-from-scratch-w-illustrator-react-emotion-39gm)

我尝试了一些其他的方法来达到这个效果，包括在使用简单的 CSS 变量之前使用 React 的上下文 API。我在使用 Context 时遇到的问题是，它只是触发了一次重新渲染，并立即切换了主题颜色，而没有应用过渡动画。

我也相信最好的方法往往是最简单的方法，在这方面使用 CSS 变量是一个胜利。唯一的问题是(哇哦，准备好了)IE 不支持它们🙄。因为这个网站是针对设计师的，他们中的绝大多数人在他们的桌面上使用现代浏览器(他们中的许多人在 Mac 上根本没有 IE 选项)或者使用他们的智能手机访问网站，我认为完全的 IE 支持是不需要的。

好了，现在开始有趣的事情。

## CSS 变量

第一步是在全局 CSS 文件的根上设置 CSS 变量，这样就可以在 DOM 中的任何地方访问它们(或者在本例中是虚拟 DOM)。

因为我试图为这个项目“部分”支持 IE11，所以我使用了回退方法，首先将元素直接设置为十六进制代码，然后用变量覆盖它。任何不支持 CSS 变量的浏览器都将忽略该覆盖。

```
/* layout.css */
:root {
  --color-bg: #f0f0f0;
  --color-text: #994cc3;
  --color-primary: #ef5350;
  --color-secondary: #0c969b;
  --color-header-bg: rgba(255, 255, 255, 0.9);
  --color-neon-fill: #ef5350;
  --color-neon-glow: none;
}

body {
  background: #f0f0f0; /* fallback */
  background: var(--color-bg);
  color: #994cc3;
  color: var(--color-text);
  transition: background 0.8s, color 0.8s;
}

h1,
h2,
h3,
h4,
h5,
h6 {
  color: #ef5350; /* fallback */
  color: var(--color-primary);
}

a {
  color: #0c969b; /* fallback */
  color: var(--color-secondary);
} 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript

通过在 CSS 中全局设置变量，可以使用 JavaScript 根据需要分配新值。我首先在我的主 layout.js 组件中创建了两个颜色主题对象——light theme 和 darkTheme。

```
// layout.js
import './layout.css';

const lightTheme = {
  '--color-text': '#994cc3',
  '--color-bg': '#f0f0f0',
  '--color-primary': '#EF5350',
  '--color-secondary': '#0c969b',
  '--color-header-bg': 'rgba(255, 255, 255, 0.9)',
  '--color-neon-fill': '#ef5350',
  '--color-neon-glow': 'rgba(255, 255, 255, 0)',
};
const darkTheme = {
  '--color-text': '#82AAFF',
  '--color-bg': '#011627',
  '--color-primary': '#c792ea',
  '--color-secondary': '#7fdbca',
  '--color-header-bg': 'rgba(1, 10, 18, 0.9)',
  '--color-neon-fill': '#FFFEFC',
  '--color-neon-glow': '#d99fff',
}; 
```

Enter fullscreen mode Exit fullscreen mode

首先，在组件内部，有两个 useState 钩子。一个用于设置模式，一个用于拨动开关。

接下来，useEffect 钩子遍历所选模式对象中的值，并将正确的颜色分配给相应的 CSS 变量。第二个参数告诉 React 仅在 currentMode 改变时重新渲染。

第二个 useEffect 在页面加载时检查 localStorage 中是否有设置为“dark”的“mode”项。如果这个项目被发现，它切换到黑暗模式。对于这个钩子，第二个参数是一个空数组，这意味着这个效果只在页面初始加载时运行一次(类似于 ComponentDidMount 在类组件中的工作方式)。

toggleTheme 函数将选中状态更新为与当前设置相反的状态，并将模式从“暗”切换到“亮”或从“亮”切换到“暗”。它还将 localStorage 中的“mode”项设置为新模式。

```
// layout.js
import React, { useState, useEffect } from 'react';

// ...

export default function Layout({ children }) {
const [currentMode, setCurrentMode] = useState('light');
const [isChecked, setIsChecked] = useState(false);

useEffect(() => {
  if (localStorage.getItem('mode') === 'dark') {
    setCurrentMode('dark');
    setIsChecked(true);
  }
}, []);

useEffect(() => {
  const theme = currentMode === 'light' ? lightTheme : darkTheme;
  Object.keys(theme).forEach(key => {
    const value = theme[key];
    document.documentElement.style.setProperty(key, value);
  });
}, [currentMode]);

const toggleTheme = () => {
  const newMode = currentMode === 'light' ? 'dark' : 'light';
  setIsChecked(!isChecked);
  setCurrentMode(newMode);
  localStorage.setItem('mode', newMode);
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为 ToggleSwitch 组件位于 Header 组件内部，所以需要将 toggleTheme 和 isChecked 函数传递到 Header 中，然后再传递到 ToggleSwitch 中。

```
//layout.js
import Header from './header';

// ...

return (
  <div className="site">
    <Header
      toggleTheme={toggleTheme}
      isChecked={isChecked}
    />
    <main>{children}</main>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

```
// header.js
import ToggleSwitch from './ToggleSwitch';

// ...

<ToggleSwitch
  toggleTheme={toggleTheme}
  isChecked={isChecked}
  id="mode"
  ariaLabel="dark mode toggle"
/> 
```

Enter fullscreen mode Exit fullscreen mode

> 切换只是一个应用了 CSS 魔法的复选框。🧙‍♂️

组件的 jsx 由一个 div (Toggle)、一个带有 type="checkbox "的输入和一个 span (Slider)组成。在复选框输入上，toggleTheme 被分配给 onChange，isChecked 被分配给 Checked。

```
import React from 'react';
import styled from '@emotion/styled';

export default function ToggleSwitch({
  toggleTheme,
  isChecked,
  ariaLabel,
  id,
}) {
  return (
    <Toggle>
      <Input
        type="checkbox"
        onChange={toggleTheme}
        checked={isChecked}
        id={id}
        aria-label={ariaLabel}
      />
      <Slider />
    </Toggle>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## 造型拨动开关

如你所见，我正在使用 CSS-in-JS🙀通过@emotion/styled 库。如果您熟悉样式化组件，它的工作方式几乎完全相同，但是包略小，而且性能明显更好(我没有实际测试过，所以我知道什么？).Emotion 还为您提供了使用 css 道具来样式化组件的选项，这在某些情况下非常有用。

要使用样式化组件，只需将 HTML 标记重命名为语义上有意义的名称，然后定义新标记应该使用哪些原生 HTML 元素以及反勾号内每个元素的 CSS。API 支持类似于 SCSS 的嵌套，样式的作用域是组件！

就我个人而言，我喜欢这让我的 JSX 标记变得如此语义化和简单。不要再在引导程序的每个元素上插入 14 个不同的类名，也不要再写恶心的类名，比如" header _ _ toggle-switch-dark-mode " ala BEM。

```
const Toggle = styled.div`
  position: relative;
  &:after {
    content: '☀️';
    font-size: 18px;
    position: absolute;
    top: 7px;
    left: 37px;
  }
  &:before {
    content: '🌙';
    font-size: 18px;
    position: absolute;
    top: 7px;
    left: 6px;
    z-index: 1;
  }
`;
const Input = styled.input`
  position: absolute;
  left: 0;
  top: 0;
  z-index: 5;
  opacity: 0;
  cursor: pointer;
  &:hover + span:after {
    box-shadow: 0 2px 15px 0 rgba(0, 0, 0, 0.2);
  }
  &:checked + span {
    background: #4a5b90;
    &:after {
      transform: translate3d(32px, 0, 0);
    }
  }
`;
const Slider = styled.span`
  position: relative;
  display: block;
  height: 32px;
  width: 64px;
  border-radius: 32px;
  transition: 0.25s ease-in-out;
  background: #3a9df8;
  box-shadow: 0 0 1px 1px rgba(0, 0, 0, 0.15);
  &:after {
    content: '';
    position: absolute;
    border-radius: 100%;
    top: 0;
    left: 0;
    z-index: 2;
    background: #fff;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
    transition: 0.25s ease-in-out;
    width: 32px;
    height: 32px;
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

CSS 变量可以分配给任何元素，点击切换开关时颜色会改变。

变量可以全局赋值...

```
/* layout.css */
body {
  margin: 0;
  line-height: 1;
  background: #f0f0f0;
  background: var(--color-bg);
  color: #994cc3;
  color: var(--color-text);
  transition: background 0.8s, color 0.8s;
} 
```

Enter fullscreen mode Exit fullscreen mode

在组件内部使用 CSS-in-JS。

```
// header.js
const MenuButton = styled.button`
  background: none;
  border: none;
  padding: 0;
  margin: 0;
  .hamburger {
    fill: #994cc3;
    fill: var(--color-text); 
    transition: fill 0.5s;
    height: 40px;
  }
  @media screen and (min-width: ${bpMed}) {
    display: none;
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！我希望你发现这信息丰富。

接下来，我将记录我如何创建闪烁的霓虹灯 SVG 动画。

第 2 部分现已推出👇

[![ninjasoards](img/e2f45280c3274ac2307a36916294a19b.png)](/ninjasoards) [## 使用 Illustrator，React & Emotion 从头开始制作闪烁的霓虹灯 SVG 动画

### 大卫·索兹 1919 年 9 月 3 日 8 分钟阅读

#react #svg #css #animation](/ninjasoards/make-a-flickering-neon-svg-animation-from-scratch-w-illustrator-react-emotion-39gm)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [大卫之路](https://github.com/davidysoards) / [路易斯维尔设计周](https://github.com/davidysoards/louisville-design-week)

### AIGA 楼 2019 年官方设计周网站

<article class="markdown-body entry-content container-lg" itemprop="text">

[![aiga](img/1d1b83afd4a7c97023e17ac81c2af76d.png)](https://github.com/davidysoards/louisville-design-week/blob/master/simg/aiga-icon.png)

# 2019 年 AIGA 楼设计周

## 盖茨比（姓）

Gatsby 是一个静态站点生成器，用于 **React** 并使用 **GraphQL** 来查询数据，通常来自 Markdown 文件(JAMStack)。它为开发人员提供了动态 web 应用程序的所有好处，并以超快的 HTML、CSS 和普通 JS 的形式提供。

## <g-emoji class="g-emoji" alias="woman_singer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f469-1f3a4.png">👩‍🎤</g-emoji>情感 CSS-in-JS 库

我用@emotion/styled 做了大部分的造型。[https://emotion.sh/](https://emotion.sh/)

## 集成

该站点从 Eventbrite API 获取客户端数据。电子邮件注册表单发布到 Mailchimp。所有其他数据都存储在本地的降价文件中。

## <g-emoji class="g-emoji" alias="dizzy" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ab.png">💫</g-emoji> Netlify

该站点在[https://louisvilledesignweek.com](https://louisvilledesignweek.com)部署了 Netlify

</article>

[View on GitHub](https://github.com/davidysoards/louisville-design-week)