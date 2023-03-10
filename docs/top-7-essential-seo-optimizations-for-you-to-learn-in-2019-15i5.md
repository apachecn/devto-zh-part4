# 🔎2019 年你需要学习的 7 大 SEO 优化

> 原文：<https://dev.to/lampewebdev/top-7-essential-seo-optimizations-for-you-to-learn-in-2019-15i5>

出于某种原因，SEO 并不是所有 web 开发人员都关注的。实际上，对于一个公司来说，SEO 可能是一个显著的优势。

在这篇文章中，我介绍了一些简单快捷的实现 SEO 优化的方法。如果你喜欢这篇文章，如果我应该做一个系列来深入研究 SEO，请在下面评论。

### 💡让图像可以被搜索引擎读取

搜索引擎不知道你的图片向用户展示了什么。让搜索引擎理解你的图片是你的工作。为此，您需要在`alt`属性中编写一个描述。它应该是一个简短的描述，不超过 155 个字符。请记住，屏幕阅读器也使用描述。不要在`alt`属性中嵌入其他内容，不要使用大量的关键字。这两种行为都会降低你的搜索引擎优化分数。

❌的一些坏例子

```
<img src="/dog.png">
<img src="/dog.png" alt="">
<img src="/dog.png" alt="dog">
<img src="/dog.png" alt="animal dog ball red"> 
```

Enter fullscreen mode Exit fullscreen mode

✔Some 好例子

```
<img src="/dog.png" alt="A dog that play with a red ball">
<img src="/billGates.png" alt="Bill Gates, former CEO of Microsoft">
<img src="/123-img.png" alt="Two girls playing on an playground"> 
```

Enter fullscreen mode Exit fullscreen mode

### 💡使用标题标签

这一条很重要，但经常没有正确实现或根本没有实现。不设置标题会极大地伤害你的 SEO。它被用在很多地方:

*   搜索引擎结果
*   书签
*   社交网络
*   浏览器标签标题

#### 如何为你的页面写好标题？

最佳长度约为 50 到 70 个字符。谷歌的搜索结果页面有一个固定的每一个结果是 600 像素。你的标题最好在 60 个字符左右。每页都有一个唯一的名称也很重要。一个好的格式是:

```
<head>
    
        Primary Keyword - Secondary Keyword | Brand Name
    
</head> 
```

Enter fullscreen mode Exit fullscreen mode

不要做太多的关键词。如果你的标题中有太多的关键词，搜索引擎将不会显示你的搜索结果。此外，请认为标题是为您的用户/客户准备的。如果你的标题过于 SEO 优化，没有任何重要信息，那么没有人会点击你的搜索结果。

#### 封号的好例子

```
 
        Best Pizza in Vilnius: 10 Pizzas you must try 
     
```

Enter fullscreen mode Exit fullscreen mode

```
 
        Women's Shoes Sale | adidas US
     
```

Enter fullscreen mode Exit fullscreen mode

```
 
        Metric – Gold Guns Girls Lyrics | Genius Lyrics
     
```

Enter fullscreen mode Exit fullscreen mode

```
 
        JavaScript Tutorial: Learn JavaScript For Free | Codecademy
     
```

Enter fullscreen mode Exit fullscreen mode

### 💡在 HTML 标签中声明正确的语言

幸运和不幸的是，大多数样板文件和框架将`lang`属性添加到 HTML 标签中，但是它们通常默认将`en`作为值添加到标签中。这向搜索引擎表明你网站上的内容是英文的。在最坏的情况下，谷歌会把你的搜索结果展示给说英语的观众，人们会立即返回结果页面。这将伤害你的搜索引擎优化得分，你会排名下降。在最坏的情况下，搜索引擎将不会显示你的结果。始终根据内容语言设置您的`lang`属性。

英语:

```
<html lang="en"> 
```

Enter fullscreen mode Exit fullscreen mode

对于德语:

```
<html lang="de"> 
```

Enter fullscreen mode Exit fullscreen mode

为了润色

```
<html lang="pl"> 
```

Enter fullscreen mode Exit fullscreen mode

诸如此类。

### 💡在锚点标签中使用`nofollow`

搜索引擎会跟踪你网站上的每个锚标签。如果你有一个恶意或垃圾网站的链接，它也会降低你网站的排名。将`nofollow`添加到用户生成的链接和付费链接中是一个很好的做法。

```
<a href="http://www.unknown.com/" rel="nofollow">Paid or not trusted content</a> 
```

Enter fullscreen mode Exit fullscreen mode

### 💡使用`rel="canonical"`

想象以下链接:

*   [https://example.com/one/url](https://example.com/one/url)
*   [https://example.com/url/one](https://example.com/url/one)
*   [https://example.com/one-url](https://example.com/one-url)
*   [https://example.com/url-one](https://example.com/url-one)

每个 URL 指向相同的内容。对于你，作为一个人类，很容易看出这是相同的内容，但对于搜索引擎来说，这是 4 个完全不同的链接。搜索引擎可能会认为这是垃圾邮件，或者如果有人只是复制了你的内容，搜索引擎不知道什么是原始的，什么样的 URL 应该显示给用户。这就是为什么添加`rel=" canonical "`是必不可少的。它告诉搜索引擎这个链接是内容的主拷贝，它应该索引这个 URL。
要告诉搜索引擎哪个 URL 是主拷贝，你需要在标题中放入
一个`link`标签。对于上面的例子，它可能是这样的:

```
<link rel="canonical" href="https://example.com/one/url"> 
```

Enter fullscreen mode Exit fullscreen mode

你需要把这个添加到上面所有的链接。也可以将它添加到带有实际链接的页面中。这被称为自引用规范标记。

### 💡在标题中添加视口元标记

这不会直接影响你的搜索引擎优化分数，但会让你的网站更适合手机使用。几乎所有的搜索引擎都将手机友好页面排在非优化页面的前面。让你的网站有反应的一个简单方法是设置 viewport meta 标签。

```
<meta name="viewport" content="width=device-width, initial-scale=1”> 
```

Enter fullscreen mode Exit fullscreen mode

内容属性的第一部分“width=device-width”告诉浏览器呈现页面以适应设备的屏幕宽度。第二部分，“initial-scale=1”，指示浏览器在横向显示页面时使页面尽可能宽。

### 💡使用媒体查询

在过去几年中，有一种趋势，即所有搜索引擎都会对显示正确内容和易于在所用设备上阅读的内容的网站进行排名。这意味着对于 web 开发人员来说，让所有设备都使用一种字体大小可能会是一个问题，因为字体在特定设备上要么太大，要么太小。媒体询问是实现这一目标的最佳方式。

```
 p {
    size: 1rem
  }
@media (max-width:768px) {
  p {
    size: 1.2rem
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你需要测试一下，看看你的内容。在这里没有简单的方法来获得好的价值观。它们将取决于字体和内容本身。

如果你能为我做以下事情，那将对我有所帮助！
去 [Twitch](https://dev.to/twitch_live_streams/lampewebdev) 给我留个关注！如果只有少数人会这样做，那么这对我意味着整个世界！❤❤❤😊

**👋说你好！**[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)|[Twitch](https://dev.to/twitch_live_streams/lampewebdev)|[YouTube](https://www.youtube.com/channel/UCYCe4Cnracnq91J0CgoyKAQ)