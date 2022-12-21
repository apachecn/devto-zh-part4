# em 到底是什么？

> 原文：<https://dev.to/kayla/what-even-is-em-4j64>

##### 虽然在定义字体大小时使用`em`或`rem`是最好的做法，但开发人员通常不确定使用哪一种，甚至不知道它们是如何工作的。在本帖中，我们将讨论什么是`em`和`rem`以及它们的区别，这样你就可以选择最适合你需求的一个。

在发表这篇博客之前，我在 Twitter 上做了一个小测验。以下是该调查的结果:

> ![Kayla HalloSween 👻 profile image](img/3313244c6da5b1e707b4e80c819febc4.png)凯拉·哈洛斯温👻@ _ kaylasween![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)考验你 CSS 知识的时候到了！
> 
> 假设你想做一个按钮，但是你想用:
> 
> button {
> font-size:2em；
> 填充:1em
> }
> 
> 假设继承了 16px，那么 px 中字体大小和填充的值是多少？2019 年 7 月 23 日 22 点 50 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1153799464340971525)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1153799464340971525)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1153799464340971525)

只有 12% 的人给出了正确答案。我知道这让我困惑，但我没意识到这也让很多人困惑。所以让我们深入了解什么是`em`和`rem`以及它们是如何工作的。

## 东地中海(Eastern Mediterranean)

在 CSS 中，`em`是相对于元素字体大小的单位。如果元素没有指定的字体大小，它将继承其父元素的字体大小。我喜欢把`em`理解为“元素的度量”

让我们以一个按钮为例。

```
button {
  font-size: 2em;
  padding: 1em;
}
```

在这个例子中，我们假设按钮继承了一个`16px`字体大小。这原本会使`1em` = `16px`。

到目前为止，一切顺利。

当我们将按钮的字体大小改为`2em`时，元素的字体大小从`16px`变为`32px`(或`2em`)。这也有道理。令人困惑的是填充会发生什么。由于元素的新字体大小是`32px`，这使得按钮的填充变为`32px`，或者是`1em`的新值。

是啊，CSS 很狂野。

## 识别内存(recognition memory)

`rem`或根`em`，是相对于根(`<html>`)元素的字体大小的单位，通常为`16px`。将我自己对`em`的理解应用到`rem`，`rem`将代表“根元素的度量”

使用`rem`，改变元素的字体大小不会改变其他属性的`rem`值。它总是`1rem` = `16px`，或者根元素的字体大小。(注意:覆盖`html`元素的字体大小可能不是一个好主意，因为是用户代理样式表设置的。您可以在 Jens Oliver Meiert 的文章“用户代理样式表:基础和示例”中了解更多关于用户代理样式表的信息。)

让我们重温一下前面的例子，但是这一次，我们将使用`rem`。

```
button {
  font-size: 2rem;
  padding: 1rem;
}
```

在这种情况下，字体大小(`2rem`)是`32px`，填充(`1rem`)是`16px`，这正是您所期望的。

## 那么我应该用哪一个呢？

看情况！如果这是你所关心的事情，我建议你自己做一下研究，看看哪个最适合你的目的。

在做了一些研究之后，我更喜欢`rem`，这样我就不用担心元素的字体大小会发生意外变化，尤其是如果我在一个很多其他开发人员都接触过的系统中工作的话。

📣**鸣笛！**📣你更喜欢主要使用`em`还是`rem`，为什么？如果你学到了什么，让我知道你学到了什么！