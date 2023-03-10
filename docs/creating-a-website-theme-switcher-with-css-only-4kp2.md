# 仅用 CSS 创建网站主题切换器

> 原文：<https://dev.to/alexandersandberg/creating-a-website-theme-switcher-with-css-only-4kp2>

[想看就看之前现场](https://alexandersandberg.github.io/theme-switcher/)！

* * *

所谓的*黑暗模式*布局最近得到了很多关注和炒作，因为越来越多的公司已经实现了他们网站和产品的可选设计。自从发布以来，我个人一直在使用 macOS Mojave 的黑暗模式，我完全被它迷住了。

自从 Safari 发布了他们新的@media 功能`prefers-color-scheme` 以来，我已经看到很多人尝试在他们自己的网站上实现这个功能。这个新的媒体特性——现在火狐也支持它——给了我们一种用一些简单的 CSS 自动检测用户偏好的方法。]

然而，这些解决方案的大多数(如果不是全部的话)的共同点是需要一点点 JavaScript 来在主题之间切换。使用 [CSS 自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)，只需要监听按钮点击并在`<body>`上设置一个`dark-mode`类或数据属性。

*Buuuutttt…* 当你只用 *CSS 就能达到同样的效果时，这样做的乐趣何在*，我说的对吗？？🙌

## 魔力显现

主题切换功能背后的秘密是*一个简单的复选框*，CSS 的 *`:checked`选择器*，以及可爱的*后继兄弟组合器*，`~`。

要做到这一点，我们首先要做几件事:

*   将我们的主题页面内容包装在一个*容器*中
*   将复选框*放在 DOM 树*中与该容器处于同一级别的和*之前*

这意味着我们将做这样的事情:

```
<body>
  <input type="checkbox" id="theme-switch">
  <div id="page">
    <!-- Insert page content here -->
  </div>
</body> 
```

为了让事情变得更好更有趣，我们还将…

*   添加一个可点击的标签来替换不那么有趣的复选框

```
<body>
  <input type="checkbox" id="theme-switch">
  <div id="page">
    <label for="theme-switch"></label>
    <!-- Insert page content here -->
  </div>
</body> 
```

通过将标签的`for`值设置为与我们的复选框的`id`值相同，标签与我们的复选框连接在一起。这意味着单击标签将选中该框。*“但是标签是空的？”我听到你说。嗯，我们会说到的。*

## CSS 自定义属性

尽管没有它这也是可能的，我们将使用 *CSS 自定义属性*(又名 CSS 变量)来帮助我们进行主题化。当心【相当不错】[浏览器对这些的支持](https://caniuse.com/#feat=css-variables)。

我们将开始为我们的两个主题定义一些颜色:

```
:root {
  /* Light mode */
  --light-text: #222430;
  --light-bg: #f7f7f7;
  --light-theme: #d34a97;

  /* Dark mode */
  --dark-text: #f7f7f7;
  --dark-bg: #222430;
  --dark-theme: #bd93f9;
} 
```

接下来，我们将定义一些新的变量来存储我们的主题颜色，这取决于所选择的主题。通过这样做，以后使用我们的主题变量会方便得多，特别是如果你有一堆 CSS 要管理的话。

```
:root {
  /* Light mode */
  --light-text: #222430;
  --light-bg: #f7f7f7;
  --light-theme: #d34a97;

  /* Dark mode */
  --dark-text: #f7f7f7;
  --dark-bg: #222430;
  --dark-theme: #bd93f9;
  /* Default mode */
  --text-color: var(--light-text);
  --bg-color: var(--light-bg);
  --theme-color: var(--light-theme);
}

/* Switched mode */
.theme-switch:checked ~ #page {
  --text-color: var(--dark-text);
  --bg-color: var(--dark-bg);
  --theme-color: var(--light-theme);
} 
```

*默认模式*变量将被默认使用，你猜对了。在这种情况下，我已经将*灯光模式*设置为默认主题，但是如果喜欢的话，*黑暗模式*变量也可以。

最后，我们会发现我们神奇的*主题切换功能*！🦄

用简单的英语解释规则集，它寻找*“类`.theme-switched`的复选框，它有一个 id 为`page`“*的兄弟。这里我们用我们的*其他主题的*颜色重新声明我们之前定义的默认变量，在这里是*黑暗模式*。

*差不多就是这样！*现在我们可以使用我们的变量创建 CSS 规则*一次*，让主题切换技术做剩下的事情。

但是请记住，我们之前必须做的事情之一是将主题页面内容包装在一个容器中。这意味着你只能在容器 :
中使用你的主题变量

```
/* This won't work */
body {
  background: var(--bg-color);
  color: var(--text-color);
}

/* But this will! 🕺 */
#page {
  background: var(--bg-color);
  color: var(--text-color);
} 
```

## 那个空标签呢？

因为我们早些时候决定，我们想用主题切换器做一些花哨的✨，现在是时候了。我不会详细说明这是如何工作的，因为主题切换功能不需要它，但请让我知道——在评论或社交媒体上——我很乐意向你解释！

首先，我们添加一些新的主题变量，根据活动主题显示不同的开关标签:

```
:root {
  /* Light mode */
  --light-switch-shadow: #373d4e;
  --light-switch-icon: "🌚";
  --light-switch-text: "dark mode?";
  …

  /* Dark mode */
  --dark-switch-shadow: #fce477;
  --dark-switch-icon: "🌝";
  --dark-switch-text: "light mode?";
  …

  /* Default mode */
  --switch-shadow-color: var(--light-switch-shadow);
  --switch-icon: var(--light-switch-icon);
  --switch-text: var(--light-switch-text);
  …
}

.theme-switch:checked ~ #page {
  --switch-shadow-color: var(--dark-switch-shadow);
  --switch-icon: var(--dark-switch-icon);
  --switch-text: var(--dark-switch-text);
  …
} 
```

接下来，我们使我们的复选框不可见，同时仍然保持它在 DOM 中。`display: none;`在这里是不够的，因为这将使主题切换器对于使用键盘导航的访问者来说遥不可及，并且*可访问性规则*！❤️

```
.theme-switch {
  position: absolute !important;
  height: 1px;
  width: 1px;
  overflow: hidden;
  clip: rect(1px, 1px, 1px, 1px);
} 
```

然后我们用`content`属性将图标和文本添加到标签中。正如你所看到的，我也在那里放了一个`text-shadow`。

```
.switch-label::before {
  content: var(--switch-icon);
  font-size: 40px;
  transition: text-shadow .2s;
}

.switch-label::after {
  content: var(--switch-text);
  color: var(--switch-shadow-color);
}

.theme-switch:focus ~ #page .switch-label::before,
.switch-label:hover::before {
  text-shadow: 0 0 15px var(--switch-shadow-color);
} 
```

我添加了一些东西使它看起来更漂亮，但是如果你愿意，你可以在源代码中找到。

## 记住选中的主题

现在情况是这样的…除非我们用的是 Firefox*，否则很遗憾，在重新加载页面后，我们无法记住哪个主题是活动的，只有 CSS。为此我们需要一点 JavaScript:

```
// This code is only used to remember theme selection
const themeSwitch = document.querySelector('.theme-switch');
themeSwitch.checked = localStorage.getItem('switchedTheme') === 'true';

themeSwitch.addEventListener('change', function (e) {
  if(e.currentTarget.checked === true) {
    // Add item to localstorage
    localStorage.setItem('switchedTheme', 'true');
  } else {
    // Remove item if theme is switched back to normal
    localStorage.removeItem('switchedTheme');
  }
}); 
```

当页面重新加载时，Firefox 实际上缓存了复选框的值，记住了我们的选择。

然而，我们可能也想记住访问者选择的主题，以便他们下次再来时使用。上面的代码将帮助我们做到这一点。

## 实时预览

如果你没有点击文章开头的链接，这里有另一个链接，可以带你去 GitHub 上的[现场预览。你也可以在 GitHub 上找到所有的源代码，在](https://alexandersandberg.github.io/theme-switcher/)[项目的资源库](https://github.com/alexandersandberg/theme-switcher)中。

## 当两个主题不够用时

明与暗之间的切换很棒，但是如果你想要一个第三个主题或者第四个主题呢？那么，如果我们用一组*单选按钮*代替我们的单个复选框——每个主题一个——会怎么样呢？

[不再多说](https://alexandersandberg.github.io/theme-switcher/unlimited.html)(GitHub 上的预览)。

这也需要一些小的调整，而不是添加单选按钮，你可以在[的源代码](https://github.com/alexandersandberg/theme-switcher)中找到。

但是，由于可访问性的原因，这种方法在设计和放置开关标签时有其局限性。你可以在上面链接的预览中读到更多。

## 底线

正如我们所见，借助一些巧妙的技术和 CSS 自定义属性来实现主题切换器并不难！

问题是，如果我们需要使用一点 JavaScript *无论如何*来记住用户对未来访问的选择，为什么我们不添加几行 JavaScript 来实现主题切换器呢？

考虑到(非常)少的人在禁用 JavaScript 的情况下浏览网页，通过这种实现，他们也可以成为乐趣的一部分！

但最重要的是，只用 *CSS 解决问题真的很令人满意。*

* * *

*这篇文章最初发表在 alexandersandberg.com 的我的网站上。**