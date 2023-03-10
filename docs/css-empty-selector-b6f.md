# CSS:空选择器

> 原文：<https://dev.to/samanthaming/css-empty-selector-b6f>

[![CodeTidbit by SamanthaMing.com](img/7fdb5ced8ba11c6d10339a8bab464277.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--56W9VoDY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bv28z2ws73wn3x3ieug6.png)

通常，我们想要设计包含内容的元素。如果一个元素根本没有子元素或文本呢？很简单，你可以使用`:empty`选择器🤩

```
<p> </p><!-- NOT empty: note the blank space -->
<p></p><!-- YES empty: nothing inbetween --> 
```

```
p::before {
  font-family: "FontAwesome";
  content: "\f240";
}

p:empty::before {
  content: "\f244";
}

p {
  color: silver;
}

p:empty {
  color: red;
} 
```

## 什么算空？

当我第一次遇到这种情况时，对于这个属性认为什么是空的有些困惑。让我们坚持使用 [MDN 的](https://developer.mozilla.org/en-US/docs/Web/CSS/:empty)定义:

> empty CSS 伪类表示任何没有子元素的元素。子元素可以是元素节点，也可以是文本(包括空白)。注释、处理指令和 CSS 内容不会影响元素是否被认为是空的。

### 为空

只要没有空格，它就是一个空元素。

```
<p></p> 
```

中间的注释也被视为空元素。只要没有空格。

```
<p><!-- comment --></p> 
```

### 不空

空白被认为不是空的。即使在新的一行中，也有空白，所以不能是空的！强调这一点，因为我犯了同样的错误😅

```
<p> </p>

<p>
<!-- comment -->
</p> 
```

有子元素也算作非空

```
<p><span></span></p> 
```

## 使用`:empty`的例子

好了，让我们来看看一些使用`:empty`的真实例子。

### 在表单错误信息中使用`:empty`

这就是让我第一次发现`:empty`的例子。所以我想在我的错误信息前加上一个❌图标。但问题是图标出现了，即使我没有错误信息。但是没问题，我可以只在出现错误消息时使用`:empty`来添加图标👍

***CSS***

```
.error:before {
  color: red;
  content: "\0274c "; /* ❌ icon */
} 
```

***HTML*T3** 

```
<!-- No error message -->
<div class="error"></div>

<!-- Yes error message -->
<div class="error">Missing Email</div> 
```

***输出***

没有空

[![](img/3bcd0399c8596bd61a05af7e19d906c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--unvvWFYk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xzodchws9c0aiqf3l13.png)

用`:empty`

[![](img/afdad0f77e6a4165ce77f9daae636763.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ISFDA22X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0utd4hod0aqqd0irc5iy.png)

### 在警报中使用`:empty`

这里是另一个使用`:empty`隐藏空状态的例子。

```
.alert {
  background: pink;
  padding: 10px;
}
.alert:empty {
  display: none;
} 
```

***HTML*T3** 

```
<div class="alert"></div>
<div class="alert">Alert Message</div> 
```

***输出***

没有空

[![](img/cee08daaa69c8da66737a1cb5d11f809.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--asOtOyPf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8kti3ciiskkqo2f61k4n.png)

用`:empty`

[![](img/1a6436956614881d0a9a184f6d3d0a53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ktqJZBFv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gn8urci86ngyn71lwhmu.png)

## 浏览器支持

这方面的支持其实真的很好。它一直支持到 Internet Explorer 9🙌

*   [浏览器支持:空](https://developer.mozilla.org/en-US/docs/Web/CSS/:empty#Browser_compatibility)

## 社区实例

当我试图在一个表单中设计空的错误消息时，我发现了`:empty`。`<div class="error"></div>`。不再需要 JS，我可以使用纯 CSS👍

**💬你还知道哪些用例？**

*   *[@妄想盒子](https://twitter.com/delusioninabox/status/1157800773557899265?s=20) :* 一个移除空段落标签的 janky 间距的修复，一般是从用户创建的内容中移除。😅

*   *[@hkfoster](https://twitter.com/hkfoster/status/1157784925631856640?s=20) :* 我已经用它压缩了任意数量的随机生成的选择器，这些选择器是我在模板端无法剔除的。👍

*   *[@sumurai8](https://twitter.com/Sumurai8/status/1157784283886555137?s=20) :* 去除空段落的填充

*   *[@_ottenga](https://www.instagram.com/p/B0tm0prAUGc/) :* 很好的通知点(如果空的不可见，如果有一些数字——例如——是红色的，数字在里面)

*   *[@ stephenjbell](https://twitter.com/stephenjbell/status/1158072968955813890?s=20):*`li:empty { visibility:hidden; }`让一个空列表项在列表中间充当某种段落分隔符(没有项目符号)。

## 社区输入

*   *[@bourhaouta](https://twitter.com/bourhaouta/status/1157750024664702976?s=20) :* 还有一点关于:empty 它不选择包含空白的元素，在另一边我们有:blank 但是它还不太支持😔

*   *[@link2twenty](https://dev.to/link2twenty/comment/dmoj) :* 我喜欢看到像这样鲜为人知的功能受到关注！可能值得注意的是，当 4 级选择器展开时，空白将被包括为空🙂。

## 资源

*   [MDN 网络文档:](https://developer.mozilla.org/en-US/docs/Web/CSS/:empty)
*   [w3 学校:](https://www.w3schools.com/cssref/sel_empty.asp)
*   [CSS 招数:清空](https://css-tricks.com/almanac/selectors/e/empty/)
*   [5 个较少使用的 CSS 选择器](https://bitsofco.de/5-lesser-used-css-selectors/)
*   [空的和空白的](https://zellwk.com/blog/empty-and-blank/)
*   [freeCodeCamp:何时使用:empty 和:blank CSS 伪选择器](https://www.freecodecamp.org/news/empty-and-blank-53b9e96151cd/)
*   [为什么空状态值得更多的设计时间](https://www.invisionapp.com/inside-design/why-empty-states-deserve-more-design-time/)
*   [codrops:空](https://tympanus.net/codrops/css_reference/empty/)

* * *

**感谢阅读❤**
问好！[insta gram](https://www.instagram.com/samanthaming/)|[Twitter](https://twitter.com/samantha_ming)|[脸书](https://www.facebook.com/hisamanthaming) | [博客](https://www.samanthaming.com/blog)|[SamanthaMing.com](https://www.samanthaming.com/)