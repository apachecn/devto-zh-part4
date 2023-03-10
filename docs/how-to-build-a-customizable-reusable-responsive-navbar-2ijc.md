# 如何构建一个可定制的、可重用的和响应的导航条

> 原文：<https://dev.to/codywilliamson/how-to-build-a-customizable-reusable-responsive-navbar-2ijc>

作为一名初级开发人员，我一直在寻找增加知识的方法，即使是关于我已经学到的东西或可能有更简单解决方案的东西。了解这些东西背后的“如何”&和“为什么”是作为一名开发者成长的最好方式。我一直在努力的一件事是构建一个可以在几乎任何网站上实现的响应性导航。所以我最终一头扎进去，建立了自己的解决方案。

## 我想完成的目标

1.  它需要在任何尺寸的屏幕上都能响应。
2.  它需要可定制以适应任何页面。

看似简单，但这两个思路很重要。现代网络有很大一部分是在移动设备上观看的。对于任何网页来说，移动响应绝对应该是优先考虑的。所以，首先*总是*确保你的网站在多种屏幕宽度下看起来都很棒。第二，我想要一些我可以经常使用的东西，并且很容易在我正在做的新东西中实现。是的，这可以通过各种方式来完成(这甚至受到了[布尔玛](%E2%80%9Chttps://bulma.io/%E2%80%9C)的启发，这是一个令人惊叹的模块化框架)，但就我自己作为开发人员的理解，我想自己来做，做一些简单的事情，而没有你的典型框架那么臃肿。

## 标记:

首先，我们将在 HTML:
中创建一个基本的导航菜单

```
<header  class="navigation-wrapper">
    <nav  role="navigation"  class="navigation"  id="navBar">
        <div  class="logo-container">
            <a  class="nav-item"  id="navbar-logo">
                <img  class="logo-img" src="https://codywilliamson.com/assets/logo-3.png">
            </a>

            <div  class="nav-burger"  id="navBurger">
                <div  class="burger"></div>
            </div>
        </div>

        <div  class="nav-items"  id="navItems">
            <a  id="navItem"  href="#home"  class="nav-item">Home</a>
            <a  id="navItem"  href="#about"  class="nav-item">About</a>
        </div>
    </nav>
</header> 
```

Enter fullscreen mode Exit fullscreen mode

现在，这里发生了一些事情。首先声明:导航条可以有一百万种不同的制作方式——有些很复杂，有些很简单，有些过于复杂，有些很简单。*提示:*没关系。大多数情况下，它们都产生相同的结果。

反正所有东西都包在`nav.navigation`容器里。如果你没有使用语义 HTML，那么你就落伍了，我的朋友。接下来是两个重要的`div`容器。一个，`div.logo-container`是导航的开始，可以包含公司名称、徽标等。它还包含了我们的导航汉堡的标记，但在那之后。

第二个`div.nav-items`包含我们实际的菜单，这是我们导航的结束部分。对于这个例子，只包括两个项目，但是您可以添加更多的项目。如您所见，这仅由两个`a`链接组成。一个`ul > li`是完全可以接受的，但是在我看来不需要`ul > li > a` CSS，特别是对于这样一个简单的项目。

## 款式:

下一部分很长，但并没有你想象的那么糟糕。首先，让我们创建一个快速的 CSS 重置并添加一些 SCSS 变量:

```
// reset found @ [https://alligator.io/css/minimal-css-reset/](https://alligator.io/css/minimal-css-reset/)
html {box-sizing: border-box;font-size: 16px;}
*, *:before, *:after {box-sizing: inherit;}
body, h1, h2, h3, h4, h5, h6, p, ol, ul {margin: 0;padding: 0;font-weight: normal; -webkite-font-smoothing: antialiased; text-rendering: optimizeLegibility;}
ol, ul {list-style: none;}
img {max-width: 100%;height: auto;}
html, body {height: 100%;width: 100;}
a {text-decoration:none;}

// colors
$black: #212121;
$white: #EEEEEE;
$darker-blue: #292E49;
$light-grey: #E0E0E0;

// nav  --  you can add as many of these as you want
//          its really limitless on customization options
$nav-padding: 1rem;
$nav-horizontal-padding: $nav-padding + 1.25rem;
$nav-vertical-padding: $nav-padding;
$nav-mobile-padding: 0.2rem;
$nav-background-color: $light-grey;
$nav-item-color: $black;
$nav-item-padding: 1rem;
$nav-item-margin: 0.5rem;
$nav-item-hover-background: $darker-blue;
$nav-item-border-radius: 5px;
$nav-font-size: 1.3em;
$nav-mobile-breakpoint: 768px;  // change to what you want based on
                                // how many nav items you have 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们要做的是从我们的`nav.navigation`容器开始:

```
.navigation {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-direction: row;
  padding: $nav-vertical-padding $nav-horizontal-padding;
  font-family: Verdana, Arial, Helvetica, sans-serif;
  font-size: $nav-font-size;
  width: 100vw;
  position: fixed;
  top: 0;
  z-index: 5;
  background: $nav-background-color;
  box-shadow: 0px 2px 5px 0px rgba(0,0,0,0.75);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的许多性质是不言自明的；然而，我将指出最重要的。

首先，`justify-content: space-between`将`div.logo-container`和`div.nav-items`放在各自的末端(`logo-container`在开始，`nav-items`在结束)。如果你想切换它们，添加`flex-direction: row-reverse`。很简单。

其次，我们有:

```
width: 100vw;
position: fixed;
top: 0;
z-index: 5; 
```

Enter fullscreen mode Exit fullscreen mode

这确保了导航在任何屏幕尺寸下都占据 100%的可用屏幕宽度，并且固定在页面顶部。至于其他的，你可以根据自己的喜好随意改变——这才是重点！

现在，如果你已经选择了一个图像作为你的公司标志，添加:

```
.logo-img {
    width: 100px; // change accordingly, but be wary of spacing
    height: auto; // ensures logo retains aspect ratio
} 
```

Enter fullscreen mode Exit fullscreen mode

在`nav-items`上。在这一节中，我们实际上是在利用我们之前创建的变量，所以一定要弄乱它们来找到你喜欢的值。

```
.nav-item {
  padding: $nav-item-padding;   // creating background size on hover
  margin: $nav-item-margin;     // adding space between the items
  color: $nav-item-color;

  &:hover {
    background: $nav-item-hover-background;
    border-radius: $nav-item-border-radius;
    color: $white;
  }
}

// this styling applies if you chose to have text instead of an img
#navbar-logo {
  color: $nav-item-color;

  &:hover {
    background: none; // set to none if using a logo img
    // if you're using an text change this to
    // $nav-item-hover-background
  }
}

// lastly, let's hide our nav-burger
.nav-burger,
.burger {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你留心的话，你会注意到我并没有特别设计`div.nav-items`的风格。因为我们在我们的`.navigation`容器中设置了`flex-direction`，它为我们完成了工作。在手机上，这种情况将会改变。

我们快完成了！再多一点点。

接下来开始我们的移动造型。我首先构建了这个桌面，然后扩展到移动设备。所以我在媒体查询中使用了`max-width:`。抓紧了，这是最后一点了！首先，我们要建立我们的导航汉堡:

```
@media (max-width: $nav-mobile-breakpoint) {
    // hide nav-items on mobile
    .nav-items {
      display: none;
    }
    // time to eat
   .nav-burger {
      display: block;
      width: 40px;
      margin: 0.5em;
   }
   // here we use pseudo elements to add a top & bottom bar
  .nav-burger:after,
  .nav-burger:before,
  .burger {
    background: $nav-item-color;
    border-radius: 3px;
    content: "";
    display: block;
    height: 5px;
    margin: 7px 0;
    transition: all 0.3s ease-in;
  }
   // when active class is added, rotate burger bars into cross
  .nav-burger.active:before {
    transform: translateY(12px) rotate(135deg);
  }

  .nav-burger.active:after {
    transform: translateY(-12px) rotate(-135deg);
  }
  // when active class is added to .nav-burger, hide middle bar
  // since .burger is child of .nav-burger, make sure to
  // select .burger as a child of the .active class
  .active .burger {
    transform: scale(0);
    opacity: 0; 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那还不算太糟。现在，当我们仍然在我们的`$nav-mobile-breakpoint`媒体查询，让我们调整一些东西，使我们的导航看起来不错。

```
 .logo-container { // this contains our logo and nav-burger
    width: 100%; // stretch width across screen
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between; // logo on left, burger on right
    padding: 0; // change to your liking
  }

  .navigation {
    padding: $nav-mobile-padding;
  }

  .navigation.active {
    flex-direction: column; // when 'active class is added, changes nav.navigation to column i.e., logo-container on top, navItems on bottom
  }

  .nav-items.active {
    display: flex;
    flex-direction: column; // show navItems in column view
    text-align: center; // can align either way
    border-top: 1px solid rgba(0, 0, 0, 0.3); // divider
    width: 100%;

    .nav-item:hover {
      background: $dark-grey;
      color: $white;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的导航风格。现在添加一些快速功能。

您可以用 jQuery 做到这一点，但是我更喜欢普通的 JavaScript。如果你觉得有趣，你也可以使用 checkbox hack，只需几行额外的代码，不需要 JavaScript，但是我会把它留到另一篇文章中。

这里要注意一点:说到 JS，我是个初学者。我还在积极地学习和实践。如果有更好的实现方法，请给我写信告诉我！

这是我们的 JavaScript:

```
// enable 'active' class on nav.navigation, .nav-burger, .nav-items
document.addEventListener('DOMContentLoaded', () => {

    // get navigation elements for active class toggle
    let navbar = document.getElementById('navBar');
    let navBurger = document.getElementById('navBurger');
    let navItems = document.getElementById('navItems');
        // get all navItems with id navItem
    let navItem = document.querySelectorAll('#navItem');

    // on nav-burger click, toggle active class
    navBurger.addEventListener('click', () => {
        navbar.classList.toggle('active');
        navBurger.classList.toggle('active');
        navItems.classList.toggle('active');

        // disable body scroll
        document.body.style.overflow = 'hidden';

        // on nav-item click, remove active class and enable body scroll
        navItem.forEach( el => {
            el.addEventListener('click', () => {
                navItems.classList.remove('active');
                navBurger.classList.remove('active');
                navbar.classList.remove('active');
                    // enable scroll on body
                document.body.style.overflow = 'scroll';
            });
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们终于完成了我们的移动响应和定制导航。这里有很多，我很感谢你通读这篇博文。希望你学到了有用的东西！