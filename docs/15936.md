# 反对类型选择器的案例

> 原文：<https://dev.to/collinstommy/a-case-against-type-selectors-46nn>

最近我一直在考虑在 CSS 中使用类型选择器。以下主要是一些观点，我欢迎对这个话题的评论或更正。

所以让我们深入研究一下手头的问题，CSS 中的类型选择器。

类型选择器的一个常见用例是实施站点范围的样式。这允许你通过设置属性如颜色、字体大小和间距来保持品牌的特色。

我们举个例子。你为我们的站点创建一些样式。

```
 h1, h2, p {
    color: '#33475b';
}
h1 {
    font-size: 3.2rem;
    font-weight: bold;
    padding: 0.5rem
}

h2 {
    font-size: 2.5rem;
}

p { 
    font-size: 1.2 rem;
} 
```

所以现在我们的元素有了一些基本样式。我们想扩充一些页面。让我们从新闻部分开始。这将有一些图像和文本。

```
<section class="news">
    <article>
        <h2>We're launching our new site</h2>
        <p>Check it out for lots of new content</p>
        <img sr="myimage" alt="image" />
    </article>
</section> 
```

我想给我的`news`部分一些风格。我知道我会有不同类型的`section`元素，所以我给它一个类。是时候设计我的元素了:

```
.news {
    // p tag, have some breathing space
    p {
        padding: 0.5;
    }
    // I want my images to stand out from my text so lets give it some padding
    img { 
        padding 1rem;
    }

} 
```

很棒的东西。现在，我的页面已经为一些新内容做好了准备。

添加一些内容后，我决定我的一些文章需要头像。我想让它们连续出现。

```
<section class="news">
    <article>
        <h2>We're launching our new site</h2>
        <p>Check it out for lots of new content</p>
        <img sr="myimage" alt="image" />
    </article>
    <article>
            <h2>I'm a different article</h2>
      <p>but I need some avatars</p>
        <div class="news__row">
            <img sr="myimage" alt="image" />
            <img sr="myimage" alt="image" />    
        </div>
    </article>
</section> 
```

```
 .news {
        &__row {
            display: flex;

            img {
                border-radius: 50%;
                width: 2rem;
            }
        }
    } 
```

我打开我的网页。这就是问题的开始。我的图像继承了这种额外的填充。这种填充对于我最初设计的每次新闻更新一张图片是有意义的。它打破了我的头像设计，我不想要这个间距。

当然，我可以通过覆盖样式，增加特异性来解决这个问题:

```
 .news {
        &__row {
            ...
            img {
                ... padding: 0;
            }
        }
    } 
```

这并不理想，因为每次我添加一个不同的有图像的子元素时，我都需要覆盖这个填充。这说明了类型选择器样式的第一个问题。

## 打破未来的设计

对于我的新闻部分中需要不同`img`样式的每个新案例，我需要覆盖填充。我在这个部分设置的默认值只适用于我最初的用例。我最初的用例并不适合元素类型样式。它太具体了，更适合班级选择器。

通过使用类，我可以确保在设计元素样式时我是有意的。除了我的站点范围的排版风格，我的元素将只由我分配给它们的类来设计风格。

注意`.news__avatar`选择器。

```
 .news {
        &__row {
            display: flex;
        }

        &__avatar { // no padding to override
            border-radius: 50%;
            width: 2rem;
        }

        &__feature-image {
            padding 1rem;
        }
    } 
```

```
<section class="news">
    <article>
        <h2>We're launching our new site</h2>
        <p>Check it out for lots of new content</p>
        <img class="news__feature-image" sr="myimage" alt="image" />
    </article>
    <article>
        <h2>We're launching our new site</h2>
        <p>Check it out for lots of new content</p>
        <div class="news__row">
            <img class="news__avatar" src="myimage" alt="image" />
            <img class="news__avatar" src="myimage" alt="image" />  
        </div>
    </article>
</section> 
```

通过使用类和边界元符号:

*   我在应用到元素上的风格上更加用心
*   当阅读我的标记时，我可以看到我的元素从哪里获得样式
*   我现在可以在我的代码库中搜索`__avatar`。我在帮助其他一些可怜的开发人员，他们必须处理我的代码

另一个优点是我的元素的样式像组件。因为它们遵循 BEM 符号，所以将它们转移到可重用组件是一个更容易的任务:

```
 const NewsItem = ({ heading, text, imageUrl }) => (<article>
        <h2>{heading}</h2>
        <p>{text}</p>
        <img class="news__feature-image" sr={imageUrl} alt="image" />
    </article>); 
```

我的特征图像不再依赖于在`.news`内并且是一个`img`标签。

这导致了类型选择器样式的另一个缺点:

## 对元素类型的依赖

通过将样式绑定到一个元素类型，如果我需要将这些样式添加到另一个元素中，我就需要修改 CSS。使用我的旧元素样式，下面的标记将需要在 CSS 中进行更改

如果我瞄准`img`，我的`svg`将不会选择正确的样式。

```
 <article>
        <h2>We're launchin our new site</h2>
        <p>Check it out for lots of new content</p>
        
    </article> 
```

通过将我的样式与类选择器相关联，我可以将它应用于不同的元素类型。

```
<article>
        <h2>We're launchin our new site</h2>
        <p>Check it out for lots of new content</p>
        
</article> 
```

让我们看另一个用例。我在我的网络应用程序中使用了一个`span`。这是一个简单的后退按钮，可以触发一些 javascript。我使用类型选择器:
来设计这个样式

```
<section class="news">
    <span>Back</span>
</section> 
```

```
.news {
    span {
        color: '#0091ae';
        text-decoration: underline;
    }
} 
```

后来，让搜索引擎优化爬虫知道这是什么跨度，我改变这是锚标签。

```
<section class="news">
    <a href="mysite.com/homapege">Back</a>
</section> 
```

这里我再次打破了我的风格，需要改变我的 CSS

## 调试困难

考虑下面的代码:

```
section {
    article {
        div {
            margin-top: 1rem;
            color: '#f2545b';
            img {
                border-radius: 3px;
            }
        }
    }
} 
```

这有许多问题。由于嵌套结构，改变我的 DOM 将破坏我的设计。这些嵌套元素在我的样式和 DOM 结构之间创建了一个紧密的耦合。一个方面的改变可能需要另一个方面的改变。

过度使用这种模式会导致调试困难。查看没有类的标记会使找到适用的 CSS 变得困难。它还要求开发人员处理特殊性问题。将来，如果我将这个 CSS 重构为可重用组件的一部分，DOM 树中匹配这些元素的子元素可能会出现意想不到的后果。

当然，上面的例子有些做作。即使嵌套了单个级别的类型选择器也会对未来的标记产生意想不到的后果。

## 总之，避免过度使用类型选择器:

*   样式被绑定到特定的元素类型
*   这可能导致破坏语义标记，从而以错误的顺序实现特定的设计，例如`<h3> <h2>`
*   改变 DOM 结构会破坏设计
*   当添加新元素类型时，需要重构
*   调试可能会更加困难

## 那么有什么解决办法呢

*   倾向于类名。有意设计你的风格。有很多工具可以帮助你避免 CSS 中的名字冲突，比如 CSS 模块，BEM，styled-components 等等，
*   将类型选择器用于它们的预期目的，用于全局应用样式以保持一致的外观和感觉。
*   HTML 标签用于从语义上构建你的页面。一个`h1`应该表明你的网页的目的。一个`p`应该包含一些长格式的文本。您应该使用类来应用样式。
*   创建类以重用样式。有`.blog__header`、`hero-title`、`big-image`。

## 有弊吗？

当然，和任何事情一样，也有潜在的缺点。

*   使用 BEM 符号，您可能会发现自己使用了许多长类名:`.news__article--main`，。`news__cta--primary`
*   现在你需要给你的新元素添加一个类`<img class=“featured-image />`

也就是说，我认为类选择器的可读性提高、易于调试和更健壮的特性超过了这些缺点。

你可能会说必须给所有的元素添加一个类是一个缺点。根据我的经验，当同一个类中有多个元素时，这通常是通过编程或 CMS 来完成的。如果您有一个需要不断手动添加的元素列表，这是一个单独的问题，可能应该以某种方式自动化。

## 后记

我相信以上大部分也适用于过度使用 if `*` `>` `+`选择器。当然，这些都有它们的位置，但是当一个简单的类可以完成这项工作时，就不应该依赖它们了。在代码中使用各种选择器使得开发人员在阅读代码时必须不断地计算这些选择器在做什么。通常最好的解决方案是最简单的。