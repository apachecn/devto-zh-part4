# 妏蚚 CSS 婓晶眻睿厄啋匼笢懈笢勤啋匼

> [https://dev . to/antojsh/as-centrar-un-element-in-vertical-y-horizontal-con-CSS-43i 2](https://dev.to/antojsh/como-centrar-un-elemento-en-vertical-y-horizontal-con-css-43i2)

当我们刚进入 web 开发领域时，创作时最常见的挑战之一就是围绕容器定位元素(垂直和水平)。如果你已经有经验，这通常是一个简单的任务，但如果你没有经验，我想给你看一些很简单的方法。

#### 同中心位置

如果您知道以下情况，即父元素具有子元素，并且您希望其子元素以其父元素为中心。

```
<div class="padre">
    <div class="hijo"></div>
</div> 
```

使用 position 我们会这样做

```
.padre {
    width: 200px;
    height: 200px;
    background: red;

    position: relative; /*El padre debe estar posicionado (relative, absolute o fixed)*/
}

.hijo {
    width: 50px;
    height: 50px;
    background: blue;

    position: absolute;
    left: 50%;
    top:  50%;
    transform: translate(-50%, -50%);
} 
```

正如我们可以看到的那样，我们是绝对定位的，这意味着它与它最近的父元素`div` `.padre`一起定位，然后我们将子元素向左移动 50%，向右移动 50%，这就使子元素相对于其左上方顶点居中。

[![Alt Text](img/625ddd47d3ea22bee9e0482caa7af87d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qbc6i9bg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fd4gi18sv8dwjg2czuoz.JPG)

但这不是我们想要的，我们想要它成为绝对的焦点。为此，我们使用`transform`属性将`.hijo`—50%水平和垂直移动相对于其大小，通过这种小把戏，我们就能得到居中。

[![Alt Text](img/ee701c5f720cf96b0f120b1ff9e55fc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vppJOitT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2dspgk9l5936g91nxuf.JPG)

#### 通用中心和柔性盒

考虑到以前的`div padre`和`div hijo`的相同情况，使用`flexbox`只需按如下方式对`.padre`应用样式即可。

```
.padre {
    width: 200px;
    height: 200px;
    background: red;

    display: flex;
    justify-content: center;
    align-items: center;
} 
```

使用 flexbox，我们可以轻松地获得相同的结果

[![Alt Text](img/ee701c5f720cf96b0f120b1ff9e55fc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vppJOitT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2dspgk9l5936g91nxuf.JPG)

我希望这些小技巧非常有用，有助于你继续积累网络开发经验。