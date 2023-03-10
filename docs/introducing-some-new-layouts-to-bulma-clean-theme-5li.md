# 向布尔玛清洁主题介绍一些新的布局

> 原文：<https://dev.to/chrisrhymes/introducing-some-new-layouts-to-bulma-clean-theme-5li>

一段时间以来，我一直想写一些我在我的杰基尔主题[布尔玛清洁主题](https://www.csrhymes.com/bulma-clean-theme/)中推出的新功能，但我只是在上周末才设法更新了登录页面的布局。本文提供了更新背后的一些想法，以及如何开始的简要介绍。

## 产品页面

我用这个主题来驱动我自己的个人网站，并决定更新我的[图书页面](https://www.csrhymes.com/books)，或当时的图书页面。该页面使用了非常简单的布局，两本书都在一页上，并没有真正达到标准。我决定最好让每本书都有自己的页面，如果我抽出时间写另一本书，将来很容易在网站上添加另一本书。我认为这将是对基本主题的一个很好的补充，而不仅仅是为我自己的网站构建这些页面。

我真的很喜欢 Jekyll，因为它使用简单，但也非常强大。我决定充分利用 frontmatter，并允许您在其中设置大部分产品信息，将页面的主要内容留给文本描述。

首先，在一个`_products`目录中创建您的产品页面，因为我们稍后将使用集合。

以下是产品页面首页的一个例子。产品页面使用与其他页面相同的英雄、标题和副标题设置，但对产品代码、图像、价格、评级和功能有额外的设置。产品代码对以后很重要。

```
--------
title: Product 1 Name
subtitle: Product 1 tagline here
description: This is a product description
hero_image: /img/hero-img.jpg
product_code: ABC124
layout: product
image: https://via.placeholder.com/640x480
price: £1.99 + VAT
features:
    - label: Great addition to any home
      icon: fa-location-arrow
    - label: Comes in a range of styles
      icon: fa-grin-stars
    - label: Available in multiple sizes
      icon: fa-fighter-jet
rating: 3
-------- 
```

Enter fullscreen mode Exit fullscreen mode

特性提供了一种制作关键产品特性列表的方法，但是除了普通的光盘项目符号之外，你还可以使用字体很棒的图标来使它更有趣。

我不知道你是怎么想的，但是有时候我在决定使用什么图标上花的时间比制作页面的其他部分还要长。

我特意将产品页面的图片比例设为 4×3，因为我觉得这在不同尺寸的屏幕上效果最好。像所有的主题，如果你想改变它，你可以覆盖默认布局，如果你想要一个不同的比例。

一旦你创建了你的产品页面，你需要告诉 Jekyll 输出它们。这是通过在您的站点的`_config.yml`中添加收藏设置来完成的。

```
collections:
  products: 
    output: true
    layout: product
    image: https://via.placeholder.com/800x600
    show_sidebar: false 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您运行 jekyll build 时，它会为您输出大量的产品页面，现在我们只需要一种方法来获取它们。这就是产品类别页面的用武之地。

## 产品类别

创建一个页面，如 products.md 或 my case books.md，并将其设置为使用产品类别布局。这将生成一个产品列表，但是如果您愿意，也可以在页面的主要内容部分添加一些介绍内容。

```
--------
title: Products
subtitle: Check out our range of products
layout: product-category
show_sidebar: false
sort: title
--------

This is some page content and it will appear above the product list. 
```

Enter fullscreen mode Exit fullscreen mode

## 产品评论

产品页面的最后一项是评论。如果您想在您的产品页面上列出一些客户评论，那么您需要在 _data 中创建一个评论目录，并为每个带有评论的产品创建一个单独的文件，以产品代码命名。例如`_data/reviews/ABC124.yml`

数据文件应遵循以下格式。名称是客户名称，评级是 5 颗星中的多少颗星，标题是评论的主标题，头像是图片的链接，如果你有图片的话。如果您没有客户图像，只需忽略它，将使用用户图标来代替。最后，描述是评论的主要内容。

```
- name: Mr E Xample
  rating: 4
  title: Great product, highly recommended
  date: 01/01/2019
  avatar: https://bulma.img/placeholders/128x128.png
  description: >
    The product worked really well. I would recommend this to most people to use. Delivery was quick and reasonable. 
    Would recommend this to my friends. 
- name: Mrs R E View
  rating: 5
  title: Nice, really liked this
  date: 02/02/2019
  description: >
    The product worked exactly as described. 
```

Enter fullscreen mode Exit fullscreen mode

示例[产品类别和产品页面](https://www.csrhymes.com/bulma-clean-theme/products/)可以在主题的演示网站上看到，如果你想看看的话。

## 登陆页面布局

我在想，创建一个登陆风格的页面会很好，这样我就可以突出显示一个新项目或我正在处理的东西，与我网站上已经存在的主项目页面分开。与其创建一个新的布局，我认为最好是增强现有的页面布局，这样你就可以选择使用这些功能，如果你愿意的话。

我首先在页面顶部的 hero 中添加了一个行动号召(也称为大按钮)。这可以通过在 frontmatter 中添加 hero_link 和 hero_link_text 来使用。

```
--------
layout: page
title: Example Landing Page
subtitle: This is an example landing page with callouts
hero_height: is-large
hero_link: /page-1/
hero_link_text: Example Call To Action
-------- 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我想做一些漂亮的标注来帮助宣传你在登陆页面上谈论的任何内容的关键特征。这是从一个简单的图标和标题开始的，但是慢慢地发展到允许副标题、描述文本和行动按钮。

为了使它更加灵活，只有标题和副标题是必需的，其余的可以在必要时使用。

为了使标注可在站点的不同页面中重复使用，内容在数据文件中定义，例如 example_callouts.yml。下面显示了标注的示例结构。

```
style: is-light
items:
  - title: Example callout 1
    subtitle: Example subtitle 1
    icon: fa-space-shuttle
    description: >
      The example description text goes here and can be multiple lines.

      For example, such as this. 
    call_to_action_name: Call to action 1
    call_to_action_link: /page-1/ 
```

Enter fullscreen mode Exit fullscreen mode

样式是包含标注的英雄的样式。这利用了布尔玛英雄的风格。

然后，要在页面上显示标注，请将标注设置添加到页面 frontmatter，使用不带扩展名的数据文件名。

```
--------
layout: page
title: Example Landing Page
subtitle: This is an example landing page
callouts: example_callouts
-------- 
```

Enter fullscreen mode Exit fullscreen mode

一个示例[登陆页面布局](https://www.csrhymes.com/bulma-clean-theme/landing/)可以在主题的演示网站上看到。

## 你怎么看？

我已经尽可能地使这些附加功能易于使用和灵活。我已经更新了自述文件和主题演示站点，提供了更多信息来帮助您开始使用这些新功能。

如果你决定尝试一下这个主题，看看你是如何使用它的，以及你对如何进一步开发它有什么想法，这将是很棒的。你永远不知道，如果我得到足够多的回应，那么我甚至可能会在演示主题网站上制作一个展示页面，以突出其他人是如何使用它的。