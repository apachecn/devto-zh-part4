# 微暗主题

> 原文：<https://dev.to/luke/micro-dark-theme-5el6>

我正在重写我的个人网站，以便更容易更新和添加内容。在这个过程中，我试图让事情尽可能简单。

当我开始设计它的时候，我决定保持完全黑白的配色方案，加上一些色彩的点缀。我意识到的是，有些人可能更喜欢深色的配色方案，而不是我正在开发的明亮的白色页面。

但是在我追求简单的使命中，我决定用最简单的实现来实现我的黑暗主题。

碰巧 W3C 正在起草一个 CSS 媒体查询的新标准，该标准指示用户是否为当前网站请求了浅色或深色主题。目前有一些浏览器支持这一功能，包括 Chrome、Firefox 和 Safari。详见 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme) 。

通过将主题偏好的责任交给浏览器，一个黑暗主题的实现可以简单到几行 CSS:

```
 #root {
        background: white;
      }     

      @media (prefers-color-scheme: dark) {
        #root, img, .no-invert {
          filter: invert(100%);
        }
      } 
```

其工作方式相当简单:

*   当操作系统或浏览器“黑暗模式”被激活时，应用媒体标签中的 CSS。
*   `#root`元素从带有黑色文本的白色背景转换为带有白色文本的黑色(这需要背景颜色)。
*   根元素中的元素*再次被反转*以正常显示。**

 *[![](img/d6748f96f72b8bab2868890d6d297cb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dGVDgwtX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/58wzavoz8hkpxyuv7n74.gif)

正如所料，反转两次的元素显示正确——看到右上角的小球了吗:

[![](img/048340392e3b86f0f84758abfb76cb90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s_fRppA4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8o2p9utue50m5uwvy54f.png)

对于一些有趣的修改，其他过滤器可以与`invert()`结合使用，例如`contrast()`:

[![](img/6c4eb19951a50ea0f9b81aa69d7d31ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MuMJFKRv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ai9ph9tmltwbkrm3gjt6.png)
[![](img/01af6c3c6b55b7f640581942ac384725.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--npxtYM7---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/al6imzkkznuhb88l7djp.png)

哦，如果你还没猜到的话，还有一个`prefers-contrast`的草稿正在制作中- [在这里找到草稿](https://drafts.csswg.org/mediaqueries-5) -这样用户就可以避免一个带有一点 CSS 的低对比度主题了！

## 鳍

感谢阅读！关注我更多这样的帖子🚀*