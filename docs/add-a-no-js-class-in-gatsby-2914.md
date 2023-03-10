# 在 Gatsby 中添加一个 no-js 类

> 原文：<https://dev.to/shubho/add-a-no-js-class-in-gatsby-2914>

过去一周我一直在和盖茨比一起工作。我正试着建立我的新博客。我以前的博客在 WordPress 上。但是在和 Hugo 一起为我的食谱博客工作之后，我爱上了静态站点生成器。我用静态 HTML 网站学习了 HTML/CSS。我的第一个网站包含 HTML 页面的文件夹。我喜欢这种方式。然而，当我开始写日记的时候，它就已经过时了。每次复制 HTML 模板，然后替换最新的内容是一件痛苦的事情。当我想要一个设计更新(这是经常发生的)，这是很痛苦的。这就是我如何发现 WordPress 的。

我第一次尝试静态站点生成器(SSG)是 Hugo。对我来说，它有一个陡峭的学习曲线，尤其是因为我不知道 Golang 中的模板语法。但是我相信 Hugo 激发了我对 Golang 的兴趣，我也因此学习了这门语言(尽管你不需要知道如何使用 Hugo 来学习)。

然而，我最熟悉的语言是 JavaScript。React 游戏我迟到了(Angular 是我的初恋)。我两个月前开始用它。一旦我对 React 的编码足够熟悉，我就想用 Gatsby - static site、JavaScript 和 React 来创建我的博客。我喜欢它。

## 问题陈述

在 Gatsby 站点的文档头中添加一个“no-js”类。

## 为什么这么做？

我有一个通过禁用 JavaScript 来检查网站的可怕习惯。不是因为我想发现网站的问题，而是想发现人们制作网站的新方式。一些网站为他们的网站创建了精彩的回退，看到他们用 JavaScript 增强体验的方式真是太棒了。其他人不用 JavaScript 就能制作出精彩绝伦的网站。对于我的 Gatsby 站点，我想看看禁用 JavaScript 后我的站点会有什么表现。

**注意** -不要从您的开发工具中禁用 JavaScript。这并不是对站点行为的实际测试，因为服务人员仍然处于负载状态。要完全禁用 JavaScript，请从浏览器设置中完成。

所以我禁用了 JavaScript，打开了我的盖茨比网站`gatsby build; gatsby serve`。一切看起来都很好。它确实显示了 Gatsby 自动添加的“这个应用程序与 JavaScript 配合得最好”。但除此之外，一切都很好。然而，一些只有 JavaScript 的功能将不起作用。例如，我有一个主题切换器。它与 CSS 变量一起工作，但是实际的切换和旧设置的记忆是 JavaScript。所以切换器在那里，但是用户点击它，什么也没有发生。如果所有的 JavaScript 功能都没有显示出来不是很好吗？许多网站通过给`<head>`元素添加一个`no-js`类来处理这个问题。

## 问题多多

默认情况下，`no-js`类被添加到 HTML 中。用 JavaScript 来代替。JS 代码是下面的三个 liner。

```
window.addEventListener('DOMContentLoaded', function() {  
document.documentElement.className =  
document.documentElement.className.replace("no-js","js");  
}); 
```

1.  我使用 react-helmet 库来管理头部。使用`<Helmet>`的`htmlAttributes`属性，我已经在管理`class`属性。所以我在那里加了`no-js`类。我把上面的 JS 代码放在一个单独的 JS 文件中，我在`gatsby-browser.js`文件中调用它。有用吗？不。即使我的 JS 文件是源代码，react-helmet 也是动态的。它触发并保持标题更新，即使当我从我的静态 HTML 替换它。更糟糕的是，`no-js`类每次都会保留，所以即使启用了它，我所有的纯 JavaScript 行为都会消失。所以我不能加入反应头盔这个职业，因为我已经在那里管理它了。
2.  我把`no-js`作为一个体类。但是，你猜对了，我也在用反应头盔管理身体类。所以这是不行的。
3.  正如这里提到的[Customize Gatsby HTML]我复制了 html.js，将脚本内联到`<head>`标签中，并手动将类添加到`<head>`。不作为反应工作-头盔删除类。😢

## 解

所以最后我想通了。因为我想继续使用 react-helmet 来管理我的`head`类，所以我需要一些其他的属性来保存这个值。所以我给`html.js`文件中的`<head>`加了一个`data-html-class="no-js"`。然后我内联脚本，但是我没有替换类，而是做了一个`setAttribute`。下面是来自`html.js`的模板的样子(只显示 HTML 模板)。相关部分是第 3 行和第 21-28 行。

```
export default function HTML(props) {
  return ( 
    <html data-body-class="no-js" {...props.htmlAttributes}>
      <head>
        <meta charSet="utf-8" />
        <meta httpEquiv="x-ua-compatible" content="ie=edge" />
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1, shrink-to-fit=no"
        />
        {props.headComponents}
      </head>
      <body {...props.bodyAttributes}>
        {props.preBodyComponents}
        <div
          key={`body`}
          id="___gatsby"
          dangerouslySetInnerHTML={{ __html: props.body }}
        />
        {props.postBodyComponents}
        <script dangerouslySetInnerHTML={{
            __html: `
            window.addEventListener('DOMContentLoaded', function() {
                document.querySelector('html').setAttribute('data-body-class', 'js')
            });
            `
        }}/>
      </body>
    </html>
  )
} 
```

现在在 CSS 中，我可以拥有这个。

```
html[data-body-class='no-js'] .only-js {
    display: none;
} 
```

无论哪个元素是纯 JavaScript 的，我都向它们添加类`only-js`，并且当 JavaScript 被禁用时，它们的 display 属性被设置为`none`。

## 结论

HTML 很牛逼。JavaScript 很牛逼。盖茨比太棒了。我恋爱了。