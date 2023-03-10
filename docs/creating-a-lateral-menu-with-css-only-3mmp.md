# 仅用 CSS 创建横向菜单

> 原文：<https://dev.to/cadienvan/creating-a-lateral-menu-with-css-only-3mmp>

## 【TL；博士]这是演示:[代码笔](https://codepen.io/Cadienvan/pen/OeXagB)

### 在你上船阅读这篇文章之前..

这只是一个实验。Javascript 变得太快了，以至于不能考虑只使用 CSS 的横向菜单，但它仍然是一个很好的实验，可以理解如何在 web 上用简单的工具构建复杂的东西。

另一件要考虑的重要事情是:这是我的第一篇文章，如果没有多一点写作技巧就能写得那么好，我很抱歉。

### 走吧...

它开始是一个实验，但后来成为一种痴迷，所以我希望在它的基础上建立一个小系列。我们能(几乎)只用 CSS 构建所有东西吗？需要澄清的是，我正试图在没有 JavaScript 的情况下构建一切，但保持一个**可接受的浏览器兼容性**(我说的是 99%的市场份额，所以 Flexbox 可以，Grid 不行)，所以我不能考虑最新的规范和浏览器前缀的东西。我还试图构建一些足够简单的东西，以便新的前端开发人员和第一次使用 CSS 的人能够阅读和理解。

### 我是怎么做到的...

我开始接近 **:hover，:active，:focus** 选择器，但很快就清楚了这些选项不能保证足够的稳定性来进行，所以我有了一个想法:**复选框**。我在网上的某个地方读到过这种技术(可能是 CSS-Tricks，这里是 Dev.to、StackOverflow 或其他一些相关的网站)，但是所有这些例子都没有涵盖一些边缘情况，而且正如我前面所说的，这些例子太复杂了，对于从事这一领域时间不够长的人来说很难读懂。
所以我走上了这条路，开始写东西，在经历了一些失败的尝试和糟糕的想法之后..**它变得非常好**(至少对我来说足够好)。

### HTML...

为了简单起见，我没有在 UI、颜色和尺寸上下太多功夫。仅仅一些简单的标记就足以解释正在发生的事情，所以这里有:

```
<!DOCTYPE html>
<html>
  <body>
    <!-- Our Hamburger -->
    <label id="hamburger" for="main-menu">
      <span></span>
      <span></span>
      <span></span>
    </label>
    <!-- Just put some page markup to make clear we can separate label from checkbox without any issue -->
    Lorem Ipsum Dolor Sit Amet Consectetur.
    <!-- Our Lateral Menu Trigger -->
    <input type="checkbox" id="main-menu">
    <!-- Our Lateral Menu -->
    <div>
      <ul>
        <li>Lorem</li>
        <li>Ipsum</li>
        <li>Dolor</li>
        <li>Sit</li>
        <li>Amet</li>
      </ul>
    </div>
    <!-- This will be the page overlay used to close the menu -->
    <label for="main-menu"></label>

  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### CSS...

事实上，CSS 甚至更简单，因为我们可以使用一些 CSS 相邻的选择器(“+”)来适当地格式化我们的主菜单并使其工作:

```
 /* Some simple (And surely non-optimal) reset */
html, body{
  margin: 0;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

body{
  padding: 8px;
}

/* Here's our hamburger */
label#hamburger{
  margin: 16px;
  display: block;
  background-color: #DEDEDE;
  width: 48px;
  height: 48px;
  position: relative;
  cursor: pointer;
  border-radius: 50%;
}

/* And his little children */
label > span {
  position: absolute;
  left: 12px;
  width: 24px;
  height: 4px;
  background-color: #FFFFFF;
}

label > span:first-child{
  top: 14px;
}

label > span:nth-child(2){
  top: 22px;
}

label > span:last-child{
  top: 30px;
}

/* Here's our invisible checkbox */
input[type="checkbox"]{
  margin: -1px;
  padding: 0;
  width: 1px;
  height: 1px;
  overflow: hidden;
  clip: rect(0 0 0 0);
  clip: rect(0, 0, 0, 0);
  position: absolute;
}

/* We put our adjacent div - the main menu - left enough to make it invisible */
input[type="checkbox"] + div{
  position: fixed;
  width: 10vw;
  height: 100vh;
  transition: left 0.5s ease;
  top: 0;
  left: -10vw;
  z-index: 10;
  background-color: #DEDEDE;
}

/* And there we have our overlay, which will stay right below the main menu (Z-index lower than main-menu's), allowing us to close it with a click. In fact, we are using the same trick as we did earlier to open the menu */
input[type="checkbox"] + div + label{
  position: fixed;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: -100vw;
  cursor: pointer;
  z-index: 9;
  transition: left 0.3s ease;
  background-color: black;
  opacity: 0.2;
}

/* Aaaaand.. Here we show both the main menu and the overlay */
input[type="checkbox"]:checked + div, input[type="checkbox"]:checked + div + label{
  left: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以你有它..通过点击汉堡包标签，我们实际上选中了我们的触发复选框，因此，允许我们的横向菜单和他的相邻覆盖物毫不费力地落入视图中。事实上，我们甚至可以重新安排我们的代码，使我们的菜单可以从任何一个方向拖放(有些类呢？).

## 提高一点..

我想我们都同意，不是每个菜单都来自左边，所以我们怎样才能把事情变得更好，让我们的菜单可以重复使用呢？

很简单，我们可以在我们的复选框中添加一些类，使我们的菜单可以从任何我们想要的地方放下。
**首先第一件事**:我们必须从我们的主菜单和标签中删除*左*、*上*和*过渡*属性的每个实例，并添加一些 CSS 来正确呈现每种情况:

```
input[type="checkbox"] + div{
  position: fixed;
  width: 10vw;
  height: 100vh;
  z-index: 10;
  background-color: #DEDEDE;
}

input[type="checkbox"] + div + label{
  position: fixed;
  width: 100vw;
  height: 100vh;
  cursor: pointer;
  z-index: 9;
  background-color: black;
  opacity: 0.2;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以处理我们的 4 种情况:**左，右，上，下。**

```
/* From Left */
input[type="checkbox"].from-left + div, input[type="checkbox"].from-left + div + label{
  transition: left 0.5s ease;
  top: 0;
}

input[type="checkbox"].from-left + div{
  left: -10vw;
}

input[type="checkbox"].from-left + div + label{
  left: -100vw;
}

input[type="checkbox"].from-left:checked + div, input[type="checkbox"].from-left:checked + div + label{
  left: 0;
}

/* From Right */
input[type="checkbox"].from-right + div, input[type="checkbox"].from-right + div + label{
  transition: right 0.5s ease;
  top: 0;
}

input[type="checkbox"].from-right + div{
  right: -10vw;
}

input[type="checkbox"].from-right + div + label{
  right: -100vw;
}

input[type="checkbox"].from-right:checked + div, input[type="checkbox"].from-right:checked + div + label{
  right: 0;
}

/* From Top */
input[type="checkbox"].from-top + div, input[type="checkbox"].from-top + div + label{
  transition: top 0.5s ease;
  left: 0;
}

input[type="checkbox"].from-top + div{
  top: -100vh;
}

input[type="checkbox"].from-top + div + label{
  top: -100vw;
}

input[type="checkbox"].from-top:checked + div, input[type="checkbox"].from-top:checked + div + label{
  top: 0;
}

/* From Bottom */
input[type="checkbox"].from-bottom + div, input[type="checkbox"].from-bottom + div + label{
  transition: bottom 0.5s ease;
  left: 0;
}

input[type="checkbox"].from-bottom + div{
  bottom: -100vh;
}

input[type="checkbox"].from-bottom + div + label{
  bottom: -100vw;
}

input[type="checkbox"].from-bottom:checked + div, input[type="checkbox"].from-bottom:checked + div + label{
  bottom: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

### JS...

你看了标题还是什么？没有 JS！

### 结论...

这是一个很好的实验，事实上我喜欢实验和构建只有 CSS 的东西。当然，正如我前面所说的，JS 中一个简单的类切换可以使我们的主菜单更加简单，但我仍然认为这是一个很好的方式来证明 CSS 可以做许多我们最初没有想到的事情。

我希望你喜欢我的第一篇帖子，并希望在未来写更多。