# 是真的吗！重要吗？

> 原文：<https://dev.to/kayla/is-it-really-important-pjn>

##### 我们知道不要在我们的 CSS 中使用`!important`，但这是为什么呢？我们将深入研究`!important`规则的作用，为什么不使用它，以及何时使用它是可以接受的。

## 什么是特异性？

在我们进入`!important`之前，我们需要了解一点关于特异性的知识。

浏览器使用特异性来确定将哪个 CSS 属性应用于元素。选择器越具体，应用 CSS 的可能性就越大。元素 id 选择器(`#id`)比类选择器(`.class`)更可能被应用，而类比元素选择器(`h2`)更可能被应用。 [Estelle Weyl](http://www.standardista.com) 对此有一个可爱的形象描述，叫做“CSS 特异性”

[![An illustration describing CSS specificity using fish. This goes from least specific to most specific. I recommend checking out Mozilla's MDN for more information on specificity. I will have it linked at the end of the article.](img/eed1c4835fb2a7d569471767725293a3.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--DFqWSrUg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ysnxb41w3tum9462sz0x.png) <small>图片来自【www.specifishity.com】。</small>
*有关特异性的更多信息，请查看关于特异性的 [MDN 文章](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)。*

## 什么是`!important`？

`!important`后缀是“一环治天下”的解决方案，它将你的风格强加在一个元素上。它重写元素上该属性的任何其他声明。最初，这是为了让用户出于偏好或可访问性的原因覆盖网站的样式表。

**热门话题:**我猜它流行起来是因为人们意识到使用`!important`比学习 CSS 如何工作更容易。

## 为什么不应该用`!important`？

它覆盖了自然的 CSS 特异性，并会对样式的层叠方式产生负面影响。这也使得你的 CSS 更难维护。Nick Rollins 在他的文章[“CSS！重要提示:不要使用它。改做这个吧。”](https://uxengineer.com/css-specificity-avoid-important-css/#what-!important-do)他举了一个开发者将一个`blockquote`元素中的所有东西都变成斜体的例子。

```
blockquote * { 
  font-style: italic; 
} 
```

它在第一次循环中不起作用，所以我们只需在末尾添加`!important`。

```
<blockquote> 
  <p>Don't write lazy CSS</p>
  <cite>- Nick Rollins</cite>
</blockquote>

<style>
  blockquote * { 
    font-style: italic !important; 
  }
</style> 
```

瞧！非常管用。

六个月后，另一个开发人员需要确保引用不是斜体。使用标准的 CSS 特异性实践，该开发人员无法从`<cite>`标签中移除斜体。覆盖一个`!important`的唯一选择是用一个`!important`，正如我们在上面 Estelle 的例子中看到的。

```
cite { 
  font-style: normal !important; 
}
```

现在，我们有了一个循环，所有的东西都需要使用`!important`来覆盖，而特殊性失去了所有的意义。

*是的，从技术上来说，如果每件事都有`!important`，我们就依赖于特异性，但关键是如果每件事都很重要，那么什么都不重要。生活中也是如此！*

## 什么时候应该用`!important`？

在大多数情况下，使用`!important`是不必要的。您几乎总是可以使用特殊性来将特定的样式应用于元素。如果您正在使用它，您当然应该有一个很好的理由来取消用户对该元素样式的控制。

我认为使用`!important`的最佳时机是在用户的浏览器样式表中。用户应该有能力覆盖任何可能对他们有害的风格，能够感知或理解网站上发生的事情。*如果你对在浏览器中应用样式表感兴趣，例如，Safari 允许你在首选项>高级>样式表中使用你自己的样式表。*

**📣你怎么想呢?有没有其他正当理由使用！重要吗？**

[未画](https://undraw.co/illustrations)的标题插图。大声喊出来[艾玛·韦德金德](https://dev.to/emmawedekind)激励我使用那些插图！