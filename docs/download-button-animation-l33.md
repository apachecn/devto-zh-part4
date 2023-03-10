# 下载按钮动画

> 原文：<https://dev.to/codedgar/download-button-animation-l33>

每次我看到一个动画或者一个微操作，我都会问自己做类似的事情要花多长时间。所以当我看到这张来自神奇的巴勃罗·斯坦利的 GIF 时，我不禁想了很多如何才能达到这种效果。

[![Pablo's Stanley button animation concept](img/c38432bf410ff7b69223ceb34be99238.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LBMzAxg9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AmHdzshog-HHTz61Msj2DkQ.gif)

我将带你经历创造的过程！

## 1。考虑将概念转化为代码。

好吧，这可能是所有事情中最难的部分，因为我不得不思考，例如，移动文本不能用按钮的正常文本来完成，我如何才能在不影响网站中所有其他组件的情况下使按钮的高度变小？我不知道。

## 2。HTML。

所有的答案都很清楚。我继续为按钮创建 HTML，我想做一些简单的东西，我不想在任何地方留下空白。所以我想到了这个:

```
 <div class="cool_holder">
             <button class="coolass_button" id="coolbutton"></button>
            </div> 
```

Enter fullscreen mode Exit fullscreen mode

没那么有趣吧？那是因为我想让它变得简单。虽然这个按钮没有文字，但它会在...

## 2。CSS。

这是事情变得有点混乱的地方，因为这是一个动画，我需要有多个类来保持这些状态并一起工作，我想给出并扩展它的解释，但帖子会很大！
总之，我加了“下载”和“成功！”文本使用:before 和:after。
而对于按钮的高度，我们的朋友 cool_holder 有 100px 的高度，所以按钮可以更小但不会影响一个网站的 overrall 构图。

注意:我没有添加 CSS，因为它对于这个小帖子来说太大了哈哈。

## 3。javascript。

这是所有内容的总结，在这种情况下，它只是一堆超时，在某个时间后不断添加其他类，也许这不是最好的，但我当时没有想到其他任何东西。

# 结果！

做完所有事情后，我对结果再满意不过了，而且用途是无限的！下载页面，让它成为“帖子”或者“发布按钮”！和...嗯，也许不是无限的，但我很高兴归档一个很酷的动画，这里是:

[https://codepen.io/Codedgar/embed/JVRbJK?height=600&default-tab=result&embed-version=2](https://codepen.io/Codedgar/embed/JVRbJK?height=600&default-tab=result&embed-version=2)

这是我的第一篇帖子！所以我可能在这里和那里滑了一下，但是我想向这个伟大的社区展示我小小的成就，感谢阅读！

如果你想在 Instagram 上关注我，我们完全欢迎你！我在那里发布了我在微交互方面的进展，以及一些网络帖子 C: