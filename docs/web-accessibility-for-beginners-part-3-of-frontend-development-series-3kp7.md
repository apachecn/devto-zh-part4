# 面向初学者的 WEB 可访问性——前端开发系列的第 3 部分

> 原文：<https://dev.to/dillionmegida/web-accessibility-for-beginners-part-3-of-frontend-development-series-3kp7>

## 什么是无障碍？

顾名思义，它是可访问的东西的质量。它是容易理解和使用的东西的品质。

## 网页无障碍？

网站不仅仅是为特定的人制作的。一旦网站在全球推出，每个人都可以访问它。作为开发者，你有责任确保那些“每个人”都能够最大限度地利用网站，不管他们是否有残疾。

> 网页可访问性简单地说就是让每个人都能方便地使用网页。
> 它确保在考虑到残疾用户的情况下，可以在网络上轻松导航、创建数据、使用数据、修改数据和执行其他操作。

#### 禁用用户如:

*   使用手机等小屏幕的用户。
*   网络连接不好的用户。
*   有听力或阅读障碍的用户。
*   依赖屏幕阅读器的用户。
*   诸如此类。

无障碍的重要性怎么强调都不为过。你怎么能期望一个用户与一个他/她无法访问的网站互动呢？

作为一个开发者，在创建网站时，你应该确保满足所有公众的需求，而不是流量的限制。

重要的是从网页设计的开始就保证可访问性，并在整个设计过程中完全遵循这一点。

## 使您的网站易于访问

有许多方法可以使 web 变得可访问，包括 Web 技术、用户代理、创作工具，甚至网站。

> 一些网站被设计来检查和建议其他网站上的可访问性想法。

### 使你的网站具有可访问性的一些小技巧

#### 1。对图像使用`alt`文本

图像也应该提供' alt '(替代)文本。这些文字为图像增添了意义。文本可能包含图像的名称或图像的描述。

这些文本是必要的，因为没有它们，图像是不容易访问的。想想那些依赖屏幕阅读器的用户，没有什么可以阅读的，结果，图像的目的就被破坏了。另一个可能需要此功能的用户可能是网络连接不良的用户，在那里图像可能不会加载。这将有助于保持用户更新。
HTML 中的用法语法是

```
...
<img src='../path/to/img/blue_ball.jpeg' alt='A blue ball' />
... 
```

Enter fullscreen mode Exit fullscreen mode

如果由于某种原因图像没有加载，将显示替代文本。因此，我们并没有完全迷路。

#### 2。音频抄本

就像视觉障碍者可能无法获得图像一样，听觉障碍者也可能无法获得音频，这导致了抄本的使用。它们使用户能够理解正在说的任何内容。

设置成绩单并不困难。有[转录服务](http://www.uiaccess.com/transcripts/transcript_services.html)提供 HTML 格式的抄本。

#### 3。Web 响应能力

作为一名开发者，你不能断定你的用户会使用什么样的设备。有些人可能会选择个人电脑，有些人可能会选择手机。很大一部分用户使用手机上网。

你必须确保所有屏幕尺寸的用户都能感受到你的网站。简单的导航应该和对网站所有部分的简单访问一起提供。尽管桌面视图可能很有趣，但移动视图应该不会有太大的不同。

缺乏响应会导致网站使用中的无聊。因此，用户迁移到其他网站以获取他们正在寻找的信息。

#### 4。诸如此类。

创作工具，如谷歌 Chrome Lighthouse ( [在此阅读用法](//bolajiayodeji.hashnode.dev/introduction-to-chrome-lighthouse-cjx7x1kwe00367vs1qic3jfoi))是检查你的网站的有用工具。它还提供了可访问性的详细信息，以及如何提高网站的可访问性。

我相信我已经能够指出确保网页可访问性的重要性以及一些方法。现在，去做负责任的网站吧🚀

### 有益的文章

*   [W3.org 无障碍基础知识](https://www.w3.org/WAI/fundamentals/accessibility-intro/)
*   [Gift Egwuenu-alt 文字使用指南](https://www.giftegwuenu.com/guide-to-using-alternative-text-on-images)
*   [初学者的网页可访问性](//scotch.io/tutorials/web-accessibility-for-beginners)

感谢阅读😍

##### 你也可以看看我以前写的关于前端开发系列的文章

*   [语义 HTML -前端开发系列第 1 部分](https://dev.to/dillionmegida/semantic-html-part-1-of-frontend-development-series-nlb)
*   [基础 SEO -前端开发系列第二部分](https://dev.to/dillionmegida/basic-seo-part-2-of-frontend-development-series-2i64)