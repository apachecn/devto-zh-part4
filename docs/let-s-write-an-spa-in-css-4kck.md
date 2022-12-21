# 让我们用 CSS 写一个 SPA

> 原文：<https://dev.to/iamschulz/let-s-write-an-spa-in-css-4kck>

让我们建立一个网站。让我们来谈谈食物，因为我喜欢食物。还有，我们需要猫，因为互联网是猫做的。还有，因为是 2019 年，所以不能有页面重载。那是什么？你说，所有这些小页面使用了太多的 Vue 和 React 以及所有的 Javascripts？好吧，让我们试着不那么，让我们有你的方式。

为了有一个关于食物和猫的网站，我们需要有内容。下面是一些:

```
<main class="content">
    <section class="content__section" id="cats">
        <img class="content__entry" src="http://lorempixel.com/500/500/cats/1/">
        <img class="content__entry" src="http://lorempixel.com/500/500/cats/2/">
        <!--[...]-->
        <img class="content__entry" src="http://lorempixel.com/500/500/cats/9/">
    </section>
    <section class="content__section" id="food">
        <img class="content__entry" src="http://lorempixel.com/500/500/food/1/">
        <img class="content__entry" src="http://lorempixel.com/500/500/food/2/">
        <!--[...]-->
        <img class="content__entry" src="http://lorempixel.com/500/500/food/9/">
    </section>
    <section class="content__section" id="foodandcats">
        <img class="content__entry" src="http://lorempixel.com/500/500/food/1/">
        <img class="content__entry" src="http://lorempixel.com/500/500/cats/2/">
        <!--[...]-->
        <img class="content__entry" src="http://lorempixel.com/500/500/food/9/">
    </section>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

嗯，看看那个 Lorem 汉堡和 Ipsum 沙拉！但都是赤裸裸的 HTML。你不能这样出去，网站，打扮一下自己！

```
.content {
    &__section {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr;
        grid-gap: 16px;
        width: 100%;
    }

    &__entry {
        width: 100%;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样更好，但是我们仍然可以看到所有的内容。那不是 SPA！好吧，那么让我们隐藏我们的内容。

```
.content {
    position: relative;

    &__section {
        position: absolute;
        top: 0;
        left: 0;
        //[...]
        pointer-events: none;
        opacity: 0;
        transform: translateX(-5%);
        transition: opacity 0.3s ease-out 0s, transform 0.3s ease-out 0s;

        &:target {
            opacity: 1;
            pointer-events: all;
            transform: translateX(0);
            transition: opacity 0.3s ease-out 0.3s, transform 0.3s ease-out 0.3s;
        }
    }

    &__entry {
        width: 100%;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了。现在一切都过去了。这里发生了什么？
我们的`.content__section`元素现在绝对位于`.content`内，所以它们相互重叠。你不能看到或点击它们，因为我们也删除了它们的不透明度和它们的指针事件(小心！指针事件是可逆的，所以任何带有`pointer-events: all;`的子节点都可以再次点击)。我们可以简单地使用`display: none`，但是我们的方法允许我们将内容部分移入和移出视图。
我们恢复了`:target`伪类上的不透明度和指针事件，所以只要我们瞄准一个`.content__section`，它就会出现。
但是所有的讨论都没有任何结果，因为我们把它放在了那个`:target`伪类的后面，甚至没有一个导航来触发它。让用户输入散列网址还不够 UX 式吗？

```
<nav class="navigation">
    <ul class="navigation__list">
        <li class="navigation__item"><a class="navigation__link" href="#cats">🐱</a></li>
        <li class="navigation__item"><a class="navigation__link" href="#food">🍴</a></li>
        <li class="navigation__item"><a class="navigation__link" href="#foodandcats">🍴+🐱</a></li>
    </ul>
</nav>
<main class="content">
    <!--[...]-->
</main> 
```

Enter fullscreen mode Exit fullscreen mode

```
.navigation {
    position: fixed;
    top: 0;
    box-sizing: border-box;
    width: 100%;
    margin-bottom: 16px;
    background: rgba(indigo, .95);
    box-shadow: 0 0 5px 0 rgba(black, .5);
    z-index: 2;

    &__list {
        box-sizing: border-box;
        display: flex;
        justify-content: center;
        width: 100%;
        margin: 0;
        padding: 16px;
        list-style: none;
    }

    &__link {
        height: 2em;
        margin: 0 24px;
        padding: 8px;
        font-size: 2em;
        color: white;
        text-decoration: none;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

给你。一个漂亮的顶栏。它甚至有表情符号，所以它很酷！
它有针对相应部分的本地链接。`:target`，还记得吗？点击链接，内容显示出来。
祝贺你，你现在可以关闭这篇文章了，因为你得到了所有重要的东西，我们的网站可以假装这是一个水疗中心。或者你可以留下来进一步完善它。
现在，我们添加了所有内容以消除重新加载的需要，但是在第一次加载时我们没有显示任何有用的内容。多么粗鲁！打个招呼。

```
<nav class="navigation">
    <ul class="navigation__list">
        <li class="navigation__item"><a class="navigation__link" href="#hi">✌️</a></li>
        <!--[...]-->
    </ul>
</nav>
<main>
    <!--[...]-->
    <section class="content__section is--default" id="hi">
        <p class="hi">hi ✌️</p>
    </section>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

```
.content {
    &__section {
        //[...]

        &.is--default {
            opacity: 1;
            pointer-events: all;
            transform: translateX(0);
        }

        &:target {
            //[...]

            & ~ .is--default {
                pointer-events: none;
                opacity: 0;
                transform: translateX(-5%);
            }
        }
    }
}

.hi {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 500px;
    font-size: 3em;
    grid-column-start: 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

与普通部分的逻辑相反，但只要前面有目标部分，就会保持隐藏。这里有一个缺点:这意味着，它需要成为`<main>`中的最后一项。嘘，语义，嘘！你可能也需要注意这里的 tabindex。

最后但同样重要的是，让我们更加平滑页面过渡。

```
:root {
    scroll-behavior: smooth;
} 
```

Enter fullscreen mode Exit fullscreen mode

每当我们改变视图时，另一个 id 就会成为目标，并提示浏览器跳转到那里。毕竟，这就是内部链接的工作方式。为了隐藏跳转，我们可以在转换页面时慢慢滚动到顶部。

现在，把它们粘在一起，放入一个笔筒:
[https://codepen.io/iamschulz/embed/GbdQpz?height=600&default-tab=result&embed-version=2](https://codepen.io/iamschulz/embed/GbdQpz?height=600&default-tab=result&embed-version=2)

厉害！那么为什么人们还在使用 Javascript 呢？这个技术也有一些缺点。

*   它使用内部链接在页面之间导航，所以你不能再把它们用于它的预期用途，页面导航。
*   它带来了一大堆问题，从 tabindex 到屏幕阅读器。然而，这些似乎是可以管理的，并不比那些普通的温泉差。
*   因为我们只是在视觉上隐藏看不见的内容，而不会将其从堆叠上下文中删除，所以文档高度是由最长的页面定义的。你可以在我们的“嗨”页面清楚地看到这一点。我们可以通过使用`display: none;`来解决这个问题，但是我们不能再过渡了。我喜欢过渡。
*   只是感觉有点不舒服

但是，嘿，对于一个小的名片式的页面，或者炫耀一些食物和猫的内容，它可以做到这一点。不需要在这里建立一个完整的构建过程，不需要下载任何包，也不需要 JS。