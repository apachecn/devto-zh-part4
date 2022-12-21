# CSS Quickies: CSS 变量——或者你如何创建一个🌞白色/🌑黑暗主题容易

> 原文：<https://dev.to/lampewebdev/css-quickies-css-variables-or-how-you-create-a-white-dark-theme-easily-1i0i>

### 什么是 CSS Quickes？

我开始在 Instagram 上问我心爱的社区:“有哪些 CSS 属性让你感到困惑？”

在“CSS Quickies”中，我将深入解释一个 CSS 属性。这些是社区请求的属性。如果你也对 CSS 属性感到困惑，那么给我写信，在 Instagram 或者 T2 的 Twitter 或者下面的评论里！我回答所有问题。

如果你想度过一些有趣的时光或者你可以问我任何问题，我也可以在 [twitch.tv](https://www.twitch.tv/lampewebdev/) 上进行现场直播。

### 先说`Custom properties`又名`CSS Variables`。

终于！如果你曾经使用过 CSS 并且想要保持你的设计的一致性？或者更像是在某些页面上，你的网站有不同的填充、边距或颜色？

也许你想实现一个黑暗的主题？这是可能的，但现在变得更容易了！

当然，如果你用过 LESS 或者 SASS，那么你知道变量，现在终于原生支持了。😁

让我们来看看吧！

#### 定义一个 CSS 变量

您通过在 CSS 属性前面加上`--`来定义 CSS 变量。让我们看一些例子。

```
:root{
  --example-color: #ccc;
  --example-align: left;
  --example-shadow: 10px 10px 5px 0px rgba(0,0,0,0.75);
} 
```

你的第一个问题是:“那是什么”:root '伪类？”。
好问题！除了特异性比':root '伪类更高之外，`:root`伪类与您使用的`html`选择器一样。这意味着如果你在`:root`伪类中设置属性，它将赢得`html`选择器。

好了，剩下的就很简单了。自定义属性`--example-color`的值为`#ccc`。例如，当我们在`background-color`属性上使用自定义属性时，该元素的背景将是浅灰色。酷吧？

您可以为自定义属性(也称为 CSS 变量)赋予 CSS 中任何其他属性的所有值。比如用`left`或者`10px`等等都可以。

#### 使用 CSS 变量

现在我们知道了如何设置 CSS 变量，我们需要学习如何使用它们！

为此，我们需要学习`var()`函数。
`var()`可以有两个参数。第一个参数需要是自定义属性。如果自定义属性无效，您希望有一个回退值。要实现这一点，你只需要设置`var()`函数的第二个参数。让我们看一个例子。

```
:root{
  --example-color: #ccc;
}

.someElement {
  background-color: var(--example-color, #d1d1d1);
} 
```

这对你来说应该很容易理解。我们将`--example-color`设置为`#ccc`，然后在`.someElement`中使用它来设置背景颜色。如果出现问题，我们的`--example-color`无效，我们有一个回退值`#d1d1d1`。

如果不设置回退值，自定义变量无效，会发生什么情况？然后，浏览器将像未指定该属性一样工作，并执行其常规工作。

#### 提示和窍门

##### 多个回退值

如果您希望有多个后备值，该怎么办？所以你会认为你可以做以下事情:

```
.someElement {
  background-color: var(--first-color, --second-color, white);
} 
```

这样不行。在第一个逗号之后,`var()`将所有内容甚至逗号都视为一个值。浏览器会把它改成`background-color: --second-color, white;`。这不是我们想要的。

为了有多个值，我们可以简单地在一个`var()`中调用`var()`。这里有一个例子:

```
.someElement {
  background-color: var(--first-color, var(--second-color, white));
} 
```

现在这会产生我们想要的结果。当`--first-color`和`--second-color`都无效时，浏览器会将背景设置为`white`。

##### 我的回退值需要逗号怎么办？

例如，如果您想在回退值中设置一个`font-family`，并且需要指定不止一种字体，该怎么办？回顾之前的提示，现在应该是直截了当的。我们简单地用逗号来写它。示例时间:

```
.someElement {
    font-family: var(--main-font, "lucida grande" , tahoma, Arial);
} 
```

在这里，我们可以看到在第一个逗号之后,`var()`函数将所有内容都视为一个值。

##### 在 Javascript 中设置和获取自定义属性

在更复杂的应用程序和网站中，您将使用 javascript 进行状态管理和呈现。您还可以使用 javascript 获取和设置自定义属性。你可以这样做:

```
 const element = document.querySelector('.someElement');
   // Get the custom propety
    element.style.getPropertyValue("--first-color");
   // Set a custom propety
   element.style.setProperty("--my-color", "#ccc"); 
```

我们可以像获取和设置其他属性一样获取和设置自定义属性。是不是很酷？

#### 用自定义变量制作主题切换器

先来看看我们在这里会做什么:
[https://codepen.io/lampewebdev/embed/zYORBwe?height=600&default-tab=result&embed-version=2](https://codepen.io/lampewebdev/embed/zYORBwe?height=600&default-tab=result&embed-version=2)

##### HTML 标记

```
<div class="grid theme-container">
  <div class="content">
    <div class="demo">
      <label class="switch">
        <input type="checkbox" class="theme-switcher">
        <span class="slider round"></span>
      </label>
    </div>
  </div>
</div> 
```

这里真的没什么特别的。
我们将使用 CSS `grid`来集中内容，这就是为什么我们在第一个元素上有一个`.grid`类`.content`和`.demo`类只是为了样式。这里的两个关键类是`.theme-container`和`.theme.switcher`。

##### Javascript 代码

```
const checkbox = document.querySelector(".theme-switcher");

checkbox.addEventListener("change", function() {
  const themeContainer = document.querySelector(".theme-container");
  if (themeContainer && this.checked) {
    themeContainer.classList.add("light");
  } else {
    themeContainer.classList.remove("light");
  }
}); 
```

首先我们选择我们的`.theme-switcher`输入和`.theme-container`元素。然后我们添加一个事件监听器，监听是否有变化发生。这意味着每当您单击输入时，该事件侦听器的回调将会运行。
在`if`子句中，我们检查是否有一个`.themeContainer`以及复选框输入是否被选中。
当这个检查为真时，我们将`.light`类添加到`.themeContainer`中，如果为假，我们将删除它。

为什么我们要删除和添加`.light`类？我们现在来回答这个问题。

##### CSS 代码

由于这段代码比较冗长，我就一步一步给大家看！

```
.grid {
  display: grid;
  justify-items: center;
  align-content: center;
  height: 100vh;
  width: 100vw;
} 
```

让我们首先集中我们的内容。我们正在用 css `grid`做这件事。我们将在另一个 CSS quickies 中介绍`grid`特性！

```
:root {
  /* light */
  --c-light-background: linear-gradient(-225deg, #E3FDF5 0%, #FFE6FA 100%);
  --c-light-checkbox: #fce100;
  /* dark */
  --c-dark-background:linear-gradient(to bottom, rgba(255,255,255,0.15) 0%, rgba(0,0,0,0.15) 100%), radial-gradient(at top center, rgba(255,255,255,0.40) 0%, rgba(0,0,0,0.40) 120%) #989898; 
  --c-dark-checkbox: #757575;
} 
```

这是大量的代码和数字，但实际上我们在这里并没有做太多。我们正在准备我们的自定义属性用于我们的主题。`--c-dark-`和`--c-light-`是我选择的自定义属性的前缀。我们在这里定义了一个光明和一个黑暗的主题。对于我们的例子，我们只需要`checkbox`颜色和`background`属性，这是我们演示中的渐变。

```
.theme-container {
  --c-background: var(--c-dark-background);
  --c-checkbox: var(--c-dark-checkbox);
  background: var(--c-background);
  background-blend-mode: multiply,multiply;
  transition: 0.4s;
}
.theme-container.light {
  --c-background: var(--c-light-background);
  --c-checkbox: var(--c-light-checkbox);
  background: var(--c-background);
} 
```

这是代码的一个组成部分。我们现在明白了为什么给`.theme-container`命名了。这是我们现在拥有全局自定义变量的入口。我们不想使用特定的自定义变量。我们想要的是使用全局自定义变量。这就是我们设置`--c-background`的原因。从现在开始，我们将只使用我们的全局自定义变量。然后我们正在设定`background`。

```
.demo {
  font-size: 32px;
}

/* The switch - the box around the slider */
.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
}

/* Hide default HTML checkbox */
.switch .theme-switcher {
  opacity: 0;
  width: 0;
  height: 0;
} 
```

这只是一些样板代码来设置我们的风格。在`.demo`选择器中，我们正在设置`font-size`。这是我们切换符号的大小。在`.switch`选择器中，`height`和`width`是我们切换符号后面的元素的长度和宽度。

```
/* The slider */
.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: var(--c-checkbox);
  transition: 0.4s;
}

.slider:before {
  position: absolute;
  content: "🌑";
  height: 0px;
  width: 0px;
  left: -10px;
  top: 16px;
  line-height: 0px;
  transition: 0.4s;
}

.theme-switcher:checked + .slider:before {
  left: 4px;
  content: "🌞";
  transform: translateX(26px);
} 
```

在这里，除了直接在`.theme.container`和大量样板代码中使用它们之外，我们终于可以看到我们的自定义属性在运行。如您所见，切换符号是简单的 Unicode 符号。这就是为什么每个操作系统和手机厂商的开关看起来都不一样。你必须记住这一点。这里需要知道的重要一点是，在`.slider:before`选择器中，我们用`left`和`top`属性来移动符号。我们在`.theme-switcher:checked + .slider:before`也在这么做，但只是在`left`酒店。

```
/* Rounded sliders */
.slider.round {
  border-radius: 34px;
} 
```

这也只是为了造型。使我们的开关在拐角处变圆。

就是这样！我们现在有一个可扩展的主题切换器。✌😀

如果你能为我做以下事情，那将对我有所帮助！
去 [Twitch](https://twitch.tv/lampewebdev) 给我留个关注！如果只有少数人会这样做，那么这对我意味着整个世界！❤❤❤😊

**👋说你好！**[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)|[Twitch](https://dev.to/twitch_live_streams/lampewebdev)|[YouTube](https://www.youtube.com/channel/UCYCe4Cnracnq91J0CgoyKAQ)