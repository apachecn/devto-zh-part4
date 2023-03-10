# 无需 javascript 即可切换点击内容(没错，你没看错)

> 原文：<https://dev.to/duranenmanuel/toggle-content-on-click-without-javascript-yes-you-read-that-right-4noh>

*原贴于[Enmascript.com](https://enmascript.com/articles/2019/09/26/toggle-content-on-click-without-javascript)T3】*

通常每次我们想点击切换网站上的内容时，我们都会使用 javascript。在这篇文章中，我们将探索一种不同的方式来做到这一点，只使用 CSS 的能力。

## 背后的力量:目标选择器

没有多少人知道这一点，但是有一个奇妙的*伪类*允许我们在 URL 中存在或不存在特定散列链接时将样式应用于元素，这个*伪元素*的名称是`:target`，它是这篇短文的主角。

## 开门见山，让我们切换话题

根据上面的解释，你现在可以想象我将要做什么，所以让我们开始吧:

首先，让我们创建一个基本的 HTML 结构

```
<div>
    <p>This is some expandable content.</p>

    <p id="hashLink">
        I have expanded like a Pufferfish.
        <a href="#">Please collapse this poor fish.</a>
    </p>
</div>

<a href="#hashLink">Expand</a> 
```

还有一些 CSS:

```
#hashLink {
    display: none;
}

#hashLink:target {
    display: block;
} 
```

## 我们在这里做什么？让我们解释一下

1.  我们正在创建一个锚点，向 URL 添加一个散列值`#hashLink`(扩展内容)。
2.  我们在第二段内创建第二个锚点，以移除该锚点并保留其为空(折叠内容)。
3.  我们要切换的内容包含在一个段落中，该段落的 id 等于我们要添加到 URL 中的哈希。
4.  我们向 id 为`hashLink`的元素添加了一个`display: none;`，这样它在默认情况下是隐藏的。
5.  我们将一个`display: block;`添加到`#hashLink:target`选择器中，这就是奇迹发生的原因，当散列出现在 URL 中时，这种样式被应用，产生了点击切换的效果。

没有更多的解释，正如你所看到的，这很简单，如果你想看一个真实的例子，请查看下面的 codepen。

[在](https://codepen.io/enmanuelduran/pen/LYPawYY) [CodePen](https://codepen.io) 上看到这支来自恩马努埃尔的笔在现场演示 LYPawYY
([@恩马努埃尔杜兰](https://codepen.io/enmanuelduran))。

这个选择器是强大的，有创意的，可以随意发布你决定用它做的很酷的事情。

你也应该知道还有其他的方法来完成这个，比如使用隐藏的复选框和在`checked`状态切换内容。