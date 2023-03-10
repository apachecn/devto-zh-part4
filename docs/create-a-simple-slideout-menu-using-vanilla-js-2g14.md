# 使用 Vanilla JS 创建一个简单的滑出式菜单

> 原文：<https://dev.to/chrismademe/create-a-simple-slideout-menu-using-vanilla-js-2g14>

任何网站最重要的部分之一就是导航，尤其是响应式设计，因为你并不总是有太多的空间来放置。这就是为什么我用传统的 HTML，CSS 和 Javascript 制作了这个超级简单而有效的滑动菜单。如果你想避开 jQuery，这个[绝对适合你](http://youmightnotneedjquery.com/)。

如果你的网站很小，或者你只需要在主导航中显示几个链接，你应该考虑是否真的需要一个隐藏菜单。

### HTML

首先，为你的菜单布局标记。

```
<nav id="slideout-menu" class="slideout-menu">
    <a id="slideout-close" class="slideout-close" href="#">&times;</a>
    <ul>
        <li><a href="#" title="Our homepage">Home</a></li>
        <li><a href="#" title="About CompanyX">About Us</a></li>
        <li><a href="#" title="Services we offer">Our Services</a></li>
        <li><a href="#" title="Get in touch!">Contact Us</a></li>
    </ul>
</nav>

<button id="slideout-toggle" class="slideout-toggle" href="#">
    <span></span>
    <span></span>
    <span></span>
</button> 
```

让我们把这个分成几部分。

```
<nav id="slideout-menu" class="slideout-menu">
    ...
</nav> 
```

我们使用 HTML5 `nav`元素，带有一个用于切换的 ID(稍后将详细介绍)和一个用于样式化的类。

```
<button id="slideout-close" class="slideout-close" href="#">&times;</button> 
```

接下来，我们创建一个`button`。这将用于关闭打开的菜单。

```
<ul>
    <li><a href="#" title="Our homepage">Home</a></li>
    <li><a href="#" title="About CompanyX">About Us</a></li>
    <li><a href="#" title="Services we offer">Our Services</a></li>
    <li><a href="#" title="Get in touch!">Contact Us</a></li>
</ul> 
```

现在我们的导航链接。

```
<button id="slideout-toggle" class="slideout-toggle" href="#">
    <span></span>
    <span></span>
    <span></span>
</button> 
```

最后，我们将创建一个简单但看起来很棒的[汉堡按钮](https://en.wikipedia.org/wiki/Hamburger_button)。

### CSS

让我们让这东西看起来棒极了！

```
body {
    color: #444;
    font-family: sans-serif;
    font-size: 14px;
}

.slideout-menu {
    background-color: #eee;
    padding: 42px 0;
    position: absolute;
    left: -280px;
    transition: left .4s;
    top: 0;
    height: 100vh;
    width: 280px;
}

.slideout-menu.is-open {
    left: 0;
}

.slideout-menu ul {
    list-style: none;
    margin: 0;
    padding: 0;
}

.slideout-menu ul li {
    display: block;
    margin: 0;
    padding: 0;
}

.slideout-menu ul li a {
    color: #555;
    display: block;
    font-size: 1.4em;
    padding: 8px 24px;
    text-decoration: none;
}

.slideout-menu a.slideout-close {
    color: #888;
    display: block;
    font-size: 2em;
    padding: 24px 42px;
    position: absolute;
    right: 0;
    top: 0;
    text-decoration: none;
}

.slideout-toggle {
    display: inline-block;
    padding: 24px;
    width: 18px;
}

.slideout-toggle span {
    background-color: #888;
    display: block;
    height: 2px;
    width: 100%;
    margin: 3px 0;
} 
```

好吧，故障。首先，我们想设计菜单本身的风格。

```
.slideout-menu {
    background-color: #eee;
    padding: 42px 0;
    position: absolute;
    left: -280px;
    transition: left .4s;
    top: 0;
    height: 100vh;
    width: 280px;
}

.slideout-menu.is-open {
    left: 0;
} 
```

为了确保它从视图中隐藏，我们将它绝对定位，并将`left`的值设置为`-280px`，这恰好是菜单的宽度。如果改变宽度，请务必相应修改`left`值。这里还有一个`transition`，所以菜单看起来很漂亮，不像是砰的一声！(没人需要这个)

当然还有`is-open`类。我们将在 Javascript 中使用它来显示切换时的菜单。

```
.slideout-menu ul {
    list-style: none;
    margin: 0;
    padding: 0;
}

.slideout-menu ul li {
    display: block;
    margin: 0;
    padding: 0;
}

.slideout-menu ul li a {
    color: #555;
    display: block;
    font-size: 1.4em;
    padding: 8px 24px;
    text-decoration: none;
} 
```

菜单样式。我选择了一个简单的、间隔合理的列表(记住，我们的目标是移动用户，所以大的点击目标是必须的！)

```
.slideout-menu a.slideout-close {
    color: #888;
    display: block;
    font-size: 2em;
    padding: 24px 42px;
    position: absolute;
    right: 0;
    top: 0;
    text-decoration: none;
} 
```

接下来，关闭按钮。同样，我添加了大量的填充，以便在移动设备上使用。它看起来也很漂亮。

```
.slideout-toggle {
    display: inline-block;
    padding: 24px;
    width: 18px;
}

.slideout-toggle span {
    background-color: #888;
    display: block;
    height: 2px;
    width: 100%;
    margin: 3px 0;
} 
```

最后是汉堡。好吃。这只是一些看起来像汉堡的标签。

### Javascript

齐心协力。没有这个，我们的菜单就没什么用了。现在，在我们更进一步之前，我只想说这当然可以用 jQuery 很容易地实现，但是我想创建一些不需要任何框架的东西。

向前...

```
document.addEventListener('DOMContentLoaded', function() {
    var menu, toggleButton, closeButton;

    // Set Elements
    menu = document.getElementById('slideout-menu');
    toggleButton = document.getElementById('slideout-toggle');
    closeButton = document.getElementById('slideout-close');

    // Toggle Menu
    toggleButton.addEventListener('click', function(e) {
        e.preventDefault();
        menu.classList.toggle('is-open');
    });

    // Close Menu
    closeButton.addEventListener('click', function(e) {
        e.preventDefault();
        menu.classList.remove('is-open');
    });
}); 
```

那么，让我们来分解一下:

```
document.addEventListener('DOMContentLoaded', function() {
    ...
}); 
```

首先，让我们通过监听`DOMContentLoaded`事件来确保 DOM 已经正确加载。对于 jQuery 用户来说，这与`$(document).ready`相同。

```
var menu, toggleButton, closeButton;

// Set Elements
menu = document.getElementById('slideout-menu');
toggleButton = document.getElementById('slideout-toggle');
closeButton = document.getElementById('slideout-close'); 
```

接下来，我们将定义一些我们将使用的变量，并找到我们需要通过它们的 id 来操作的元素(正如我前面提到的)。

```
// Toggle Menu
toggleButton.addEventListener('click', function(e) {
    e.preventDefault();
    menu.classList.toggle('is-open');
}); 
```

现在我们有了我们的元素，我们将监听切换按钮上的点击。(对于 jQuery，这将是`$('#slideout-toggle').click(function(e)) {}`)然后如果不存在，我们添加`is-open`类，否则我们移除它。

在我的例子中，切换按钮的位置使得当菜单打开时你不能使用它，但是如果它是可见的，当菜单打开时点击它将关闭它。

```
// Close Menu
closeButton.addEventListener('click', function(e) {
    e.preventDefault();
    menu.classList.remove('is-open');
}); 
```

最后，我们监听关闭按钮(在菜单内)的点击，然后移除`is-open`类。

就是这样！你的下一个网站有一个很棒的幻灯片菜单。

### 演示

对于工作演示，请前往 [Codepen](http://codepen.io/chrismademe/pen/bEMQbK) :)。