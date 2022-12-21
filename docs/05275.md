# 我发现了零宽度空间的用途

> 原文：<https://dev.to/bradymholt/i-found-a-use-for-a-zero-width-space-22jm>

[![Zero-width space](img/d43afa48c1243f711b1c0860e9e61558.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ntsCXRnS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yzzqvktww2tffpca24th.png)

你知道零宽度空格吧？这是一个看不见的角色，乍一看似乎没什么用。
我对它们的大多数体验都是负面的，因为它们通常出现在我正在解析的随机文件中，并且是一个奇怪错误的原因。或者，这是复制/粘贴搜索没有结果的原因，即使我眼前有匹配的结果。

好吧，尽管我对它们漠不关心，但实际上我发现它们有很好的用途。

我最近正在构建一个用 TypeScript 编写的静态站点生成器 [xertz](https://github.com/bradymholt/xertz) 。它被用来建造这个网站。一直困扰我的是渲染 HTML 的缩进。我使用 Handlebars.js 模板，包括从 Markdown 转换而来的原始 HTML。我的模板如下所示，其中`{{ content_html }}`是原始的 HTML 位:

```
<body>
  {{> header }}
  {{> sidebar }}
  <article class="post">
    {{{ content_html }}}
  </article>
  {{> footer }}
</body> 
```

问题是，`{{ content_html }}`中的换行符不会缩进，所以实际输出看起来像这样:

```
<body>
  <header>My Site</header>
  <aside>Sidebar here</aside>
  <article class="post">
    <p>Lorem ipsum dolor sit amet, usu an justo deterruisset. Est ad discere nominati,
erroribus dissentias mei ne, appetere qualisque eloquentiam sea et.</p><img alt="An image" src="my-image.jpg"/><p>Lorem ipsum
dolor sit amet, usu an justo deterruisset. Est ad discere nominati</p>
  </article>
  <footer>My footer</footer>
</body> 
```

这是一个简单的例子。实际上情况要糟糕得多。是的，这对网页
的实际布局和渲染没有影响，但我是一名开发人员，关心源代码和它的外观。

我试图创建一个名为“indent”的 Handlebars 助手，这样我就可以缩进每一个换行符。我这样称呼它:

```
...
<article class="post">
  {{{ indent content_html 2 }}}
</article>
... 
```

助手非常简单。它只是将换行符(\n)替换为一个换行符后跟一些空格:

```
function indent(input: string, width: number) {
  const intendation = input.replace(/\n/g, "\n" + new Array(width).join("  "));
  return input.replace(/\n/g, match => match.replace(/\n/, `\n${intendation}`)
} 
```

这工作得很好，直到我的`<pre>`代码块开始看起来像这样:

```
const my_var;
    const another_var; 
```

HTML 看起来是这样的:

```
<body>
  <header>My Site</header>
  <aside>Sidebar here</aside>
  <article class="post">
    <p>Below is a code block</p><pre>const my_var;
    const another_var;</pre><p>Lorem ipsum dolor sit amet, usu an justo deterruisset. Est ad discere nominati,
    dissentias mei ne</p>
  </article>
  <footer>My footer</footer>
</body> 
```

啊哦，我的代码块缩进了。是的，因为我的代码块使用了`<pre>`,所以代码块中的任何空格都将按原样呈现。`<pre>`终究是“预格式化”的意思。

因此，我认为我需要*提示*向**缩进**助手发出信号，跳过这些`<pre>`标签中的缩进。

在`<pre>`标签中添加提示似乎是可行的，因为我使用[棱镜](https://prismjs.com/)和[标记的](https://github.com/markedjs/marked)来转换 Markdown 代码块，如:

```
```javascript
const my_var = "Hello";
``` 
```

分成`<pre>`块。修改这些标签的输出非常容易，因为您提供了一个函数，它会返回如下内容:

```
return `<pre class="${className}"><code class="${className}">${code}</code></pre>`; 
```

容易修改，是的。我想，“我可以在`<pre>`标签行的末尾添加一些字符，让我的缩进助手跳过吗？”但是由于我使用 RegEx 在我的 **indent** 助手中添加缩进，所以我只能使用单个字符在我的 Regex 中包含一个被否定的字符(例如`[^!]`)，而不必进行反向查找(反正 Javascript 不支持这些)。

好了，我只需要 Prism 添加一个字符，这个字符在`<pre>`块中的行尾是不可见的。然后我的**缩进**助手可以忽略这些。我该怎么做？

当然是零宽度空格！

现在，我的代码格式化函数在我的代码块中的换行符前面加了一个零宽度的空格。看起来是这样的:

```
const codeWithNewlineHints = code.replace(
  /\n/g,
  // Prepend each newline with a zero-width space character so we can signal to any upstream formatting to leave the formatted code alone.
  "\u200b\n"
);

return `<pre class="${className}"><code class="${className}">${codeWithNewlineHints}</code></pre>`; 
```

在我的 **indent** 助手中，我简单地忽略了换行符前包含这些字符的行。

```
const intendation = input.replace(/\n/g, "\n" + new Array(width).join("  "));
return input.replace(/\n/g, match => match.replace(/[^\u200b]\n/, `\n${intendation}`) 
```

看到那个正则表达式了吗？`/[^\u200b]\n/`表示只匹配换行符，如果它们是*而不是*前面的零宽度字符(\u200b)。因此，这样的话，缩进只会被添加到前面没有这些字符的行。

我对零宽度空格有了新的认识。