# 为 VueJS 和 GSAP 创作的手风琴作品

> 原文：<https://dev.to/takaneichinose/accordion-component-for-vuejs-and-gsap-53kd>

手风琴组件，用 VueJS 写的。动画是由 GSAP 完成的。

* * *

# 工作原理

我们就按你想要的方式设计手风琴吧。你可以把它变得丰富多彩，或者简单。

唯一的区别是插入符号。我没有像你通常用 CSS 做三角形那样做。我试图用自己的方式(很难看，而且只适用于现代浏览器)。

```
.accordion-caret {
  background-image: linear-gradient(to top right, transparent 50%, #727272 50%);
  width: 0.5rem;
  height: 0.5rem;
  transform: rotate(-45deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

我不知道，但对我来说，这种风格比传统的三角形更舒服。还有，嗯，我想要一点改变。

*   声明:请注意，我很确定有人已经这样做了。就好像我实际上并没有浏览互联网来获得代码，去做所有这些事情(除了我浏览官方文档的每个库/框架的文档)。

### Vue VM

因为我不能在我的笔上放一个真实世界的数据，所以我只是放一个要被提供的静态数据。它必须放在虚拟机上。

```
var app = new Vue({
  el: '#app',
  data: {
    // 'title' is for the header. The area that you can see.
    // 'description' is for the content. It will show after you expand an accordion menu
    // 'active' is for the flag, if the content is shown or not.
    contents: [{
      title: "'Lorem ipsum dolor sit amet',"
      description: "'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.',"
      active: false
    }, {
      title: "'Ut enim ad minim veniam',"
      description: "'Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.',"
      active: false
    } // ...
    ]
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 模板

我只是将从 VM 传递的道具循环到组件，以显示模板或手风琴的设计。

```
<div class="accordion">
  <!-- Looping of the props. -->
  <div
    class="accordion-item"
    v-for="content, i in contents"
    v-bind:class="{ 'accordion-active': content.active }"
  >
    <div class="accordion-header">
      <!-- The click event of each accordion menu -->
      <a href="#" v-on:click="expand(event, i)">
        <div class="accordion-header-div">{{ content.title }}</div>
        <div class="accordion-header-div">
          <div class="accordion-caret"></div>
        </div>
      </a>
    </div>
    <!-- We need the ref to get the DOM of the body. -->
    <div class="accordion-body" v-bind:ref="'accordion-body-' + i">
      <div class="accordion-content">{{ content.description }}</div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

*   我使用了 HTML 突出显示，这样元素可以很容易理解。同样，因为还有另一种方式来编写我们的模板。

### 动画为资料片

下面是你如何做扩展。基本上，我用 GSAP 做动画作品。老实说，它比 CSS 动画更容易使用。而且，它的过渡比原生 CSS 过渡要好。

```
// The dom of the accordion body, we will bind the GSAP animation here
let el = this.$refs['accordion-body-' + i][0];

if (this.contents[i].active === false) {
  this.contents[i].active = true;

  // The GSAP animation for the expansion of an accordion menu
  TweenLite.to(el, 1, {
    height: el.scrollHeight,
    ease: Elastic.easeOut.config(1, 0.3)
  });
} else {
  this.contents[i].active = false;

  // The GSAP animation to hide the accordion menu content
  TweenLite.to(el, 0.5, {
    height: 0,
    ease: Bounce.easeOut
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

*   想了解更多关于 GSAP 的信息，你可以点击这个链接:[格林斯托克](https://greensock.com/docs/)

*   vuej 文档:[vuej 指南](https://vuejs.org/v2/guide/)

*   补充:是的，指南本身很容易理解，当你阅读它的时候，你可以很舒服地使用 VueJS。

* * *

# 演示

[https://codepen.io/takaneichinose/embed/rXMrgv?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/rXMrgv?height=600&default-tab=result&embed-version=2)