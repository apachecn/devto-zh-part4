# 如何用 PugJS 在标题上插入跳转锚

> 原文：<https://dev.to/spekulatius1984/how-to-insert-jump-anchors-on-headings-with-pugjs-1ib5>

我绝对是猫的粉丝😺️在现实生活中，但当谈到代码这颠倒过来，我有一个偏好帕格🐶非程序员的️:pug js 是一个 JavaScript 模板引擎。它将 Pug-lang 转换成 HTML。我知道 PugJS 有更多的功能，除了简单地将 Pug-lang 转换成 HTML。目前我还没有发挥这种语言的潜力——我主要使用 PugJS 来保存我的内容。让我们来看看我最近了解到的一些让帕格发挥更大作用的方法。

如果您在静态站点上使用 Pug，您可以:

*   使用变量来设置整个站点或应用程序的内容和属性。
*   声明 Mixins 来定义 HTML 标签、属性等等。稍后会详细介绍。
*   使用继承和包含自由地组件化您的项目文件。这允许你建立一个你喜欢的项目结构。我喜欢保持与相关的 SCSS 文件相同的结构——只是为了避免项目中不必要的复杂性。
*   您可以拥有条件内容*而无需*客户端 JavaScript。一个简单的例子是:

```
- var promo_is_live = false;

if promo_is_live
    h2 Special deal!
    p Get our super special promotional offer!
    p.price Only $9.99
else
    p.price Only $19.99 
```

根据变量`promo_is_live`，该内容将动态地包含在 HTML 输出的每次编译中。如果将标志切换为 false，任何相关内容都不会包含在输出中。

这可以保持您的网站整洁，避免暴露时间敏感的信息(如每年的圣诞节促销)，同时避免每次都需要手动添加和删除促销代码和临时内容。如果你想为搜索引擎优化你的网站，避免隐藏内容会很有用。

Pug 是一个强大的模板引擎，阅读[语言参考](https://pugjs.org/language/attributes.html)有助于某些*“啊，整齐”*-时刻！下面，你会发现一个适用于日常编码的有用的 mixin 例子。

## 一个例子:使用 Pug-Mixins 给标题添加动态锚

通常，您可能想要一个`id`来允许您直接跳转到文档的某个部分。对我来说，这些通常是标题。上面的标题*一个例子:使用 Pug*给标题添加动态锚，我会写

```
h2(id="an-example-adding-dynamic-anchors-to-headings-using-Pug").
    An example: Adding dynamic anchors to headings using Pug 
```

这触发了我，因为它容易出错，作为开发人员阅读开销大，维护开销大。如果您可以避免双重内容，而是动态地从标题中生成一段文字，这不是更好吗？使用 Pug，您可以:

```
mixin h2(headline)
  - let id = headline.toLowerCase().replaceAll(' ', '-').replace(/[!\"#$%&'\(\)\*\+,\.\/:;<=>\?\@\[\\\]\^`\{\|\}~]/g, '');
  h2(id= id)
    = headline 
```

有了这个小助手，你就可以写

```
+h2('An example: Adding dynamic anchors to headings using Pug') 
```

并且它将被处理到

```
<h2 id="an-example-adding-dynamic-anchors-to-headings-using-Pug">
  An example: Adding dynamic anchors to headings using Pug
</h2> 
```

这难道不是更好的开发者体验吗？:)

## 更进一步

你也可以把标题级别作为一个参数交给混音。

```
mixin h(level, headline)
  - let id = headline.toLowerCase().replace(' ', '-').replace(/[!\"#$%&'\(\)\*\+,\.\/:;<=>\?\@\[\\\]\^`\{\|\}~]/g, '');
  section(id= id)
    #{'h' + level}= headline
    if block
      block 
```

允许您编写以下调试代码

```
+h(2, 'Section A')

+h(2, 'Section B')
  p Lorem ipsum dolor amit

+h(2, 'Section C')
  p Lorem ipsum dolor amit
  +h(3, 'Section C, Subsection A')
    p Lorem ipsum dolor amit 
```

将被编译成如下所示的 HTML】

```
<section id="section-a">
  <h2>Section A</h2>
</section>

<section id="section-b">
  <h2>Section B</h2>
  <p>Lorem ipsum dolor amit</p>
</section>

<section id="section-c">
  <h2>Section C</h2>
  <p>Lorem ipsum dolor amit</p>
  <section id="section-c-subsection-a">
    <h3>Section C, Subsection A</h3>
    <p>Lorem ipsum dolor amit</p>
  </section>
</section> 
```

这允许您动态地定义任何级别的标题。

## 汇总和来源

如上所述，语言能做的更多。混血儿只是 PugJS 强大的一面。有关更多信息，请查看语言参考。顺便说一下，如果你也是 Netlify 的粉丝，我有好消息要告诉你。当然，你可以在你的 Netlify 托管网站上使用 [Pug。](https://peterthaleikis.com/posts/how-to-use-pug-on-netlify/)

该表扬的地方就表扬:感谢 Pug 维护人员的出色工作。我必须对 Sean 说“谢谢”,他也给我指出了在 Stackoverflow 上回答我的问题的可能性。