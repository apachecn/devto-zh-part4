# 使用 Svelte & Sapper 实现流畅的页面过渡

> 原文：<https://dev.to/buhrmi/svelte-component-transitions-5ie>

更新:这篇文章已经写了 2 年多了，但是它仍然有一定的相关性。这项技术应该与 [SvelteKit](https://kit.svelte.dev) 完全相同。

当你从一个页面浏览到另一个页面时，有没有遇到过有很酷的动画的网站？例如[这个网站](https://page-transitions.com/)的作者[莎拉·德拉斯纳](https://twitter.com/sarah_edo)。通常，您会使用客户端库和聪明的 CSS 技巧来实现这类事情。这可能没什么问题，但是它增加了你网站的总下载量，而且随着你网站复杂性的增加，你的 CSS 将变得难以维护。然而，多亏了[的苗条身材](https://svelte.dev)，我们现在可以在没有任何客户端库或 CSS 欺骗的情况下做这些很酷的事情:

[![GREAT SUCCESS](img/66b7afbbe3e4ca13c2c8c831312895e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pn0lAtHY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/82y5qvdmiqh8t3ntor0g.gif) 
( [试用](https://codesandbox.io/embed/fluidui-9l3t2))

所以现在我们可以有很酷的 pwa，它们在服务器端呈现，有平滑的页面过渡，并且被编译成非常少量的 javascript。不需要客户端库、虚拟 DOM 或移动 CSS 类。Oouwee！

就这样，我们的生活结束了。因为我们还能想要什么？我们现在都应该去赞美一下 JavaScript 之神 Rich Harris。还是我们需要更多的 JavaScript 框架？我们还不够吗？我再也受不了了。让它停下来。啊啊啊啊，这就是结局。

## 如何？

这些动画都使用了 Svelte 的[交叉渐变过渡](https://svelte.dev/tutorial/deferred-transitions)。如果你以前没用过，我建议你看看[教程](https://svelte.dev/tutorial/deferred-transitions)。它将教你如何在同一个组件中交叉渐变两个元素。但是我们感兴趣的是将一个元素从一个组件**渐变到另一个组件**(在 Sapper 中，每个页面都是一个组件)。让交叉渐变过渡跨不同文件中定义的不同页面(或组件)工作的技巧是在它自己的模块/文件中创建交叉渐变过渡，并将其`import`到不同的组件中。看看这个:

在这里，我们在自己的文件中创建交叉渐变过渡，并导出`send`和`receive`函数，这样我们就可以在不同的文件中重用它们。

```
// crossfade.js
import { quintOut } from 'svelte/easing';
import { crossfade } from 'svelte/transition';
const [send, receive] = crossfade({
    duration: d => Math.sqrt(d * 300),
    fallback(node, params) {
        const style = getComputedStyle(node);
        const transform = style.transform === 'none' ? '' : style.transform;

        return {
            duration: 600,
            easing: quintOut,
            css: t => `
                transform: ${transform} scale(${t});
                opacity: ${t} `
        };
    }
});

export {send, receive}; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们使用`send`和`receive`函数。

```
// page1.svelte
<script>
  import {send, receive} from './crossfade.js';
</script>

<main>
  <h1 out:send="{{key: 'h1'}}" in:receive="{{key: 'h1'}}">Look, I'm above the image</h1>
  <img out:send="{{key: 'borat'}}" in:receive="{{key: 'borat'}}" alt='Borat' src='great-success.png'>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

然后再次使用**相同的** `send`和`receive`函数，使交叉渐变跨组件工作。

```
// page2.svelte
<script>
  import {send, receive} from './crossfade.js';
</script>

<main>
  <img out:send="{{key: 'borat'}}" in:receive="{{key: 'borat'}}" alt='Borat' src='great-success.png'>
  <h1 out:send="{{key: 'h1'}}" in:receive="{{key: 'h1'}}">Now I am below the image</h1>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

容器需要绝对定位，否则无法工作。

```
// global.css
main {
  position: absolute;
  width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

给你。感谢阅读。

如果您需要专业支持，请访问 [https://buhrmi.dev](https://buhrmi.dev) 。