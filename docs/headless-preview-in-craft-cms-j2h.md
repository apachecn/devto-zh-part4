# Craft CMS 中的无头预览

> 原文：<https://dev.to/gaijinity/headless-preview-in-craft-cms-j2h>

# Craft CMS 中的无头预览

### Craft CMS 3.2 介绍无头内容预览。这里有一个关于它如何工作的解释，以及你如何实现它

安德鲁·韦尔奇

[![Headless preview craft cms](img/1ec9cc85cf8ff4b5df493283dda578fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JBvgIX9P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/headless-preview-craft-cms.jpg)

Craft CMS 早就有一个“实时预览”功能，允许内容作者预览他们的内容发布到 web 上后的样子。

随着 [Craft CMS 3.2](https://craftcms.com/blog/craft-32) 的推出，增加的一个主要特性是“无头预览”。

这个特性允许开发者通过像 [React](https://reactjs.org/) 、 [Vue.js](https://vuejs.org/) 、[svelite](https://svelte.dev/)等前端框架将他们的页面作为[单页面应用程序(SPA)](https://medium.com/@NeotericEU/single-page-application-vs-multiple-page-application-2591588efe58) 来使用。也可以让手工内容预览。

## 为什么需要重写

尽管在“内容管理系统”中没有一个内容管理引擎的承诺，但是所有的内容管理系统都不仅仅是管理内容，还可以将内容作为网页来管理。

[![Craft cms headless preview horseman](img/2344be92e2aa749726d1d52cdf0e6f4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q1ssu_We--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_992x558_crop_center-center_82_line/craft-cms-headless-preview-horseman.jpg)

CMS 租赁内容可能是出于方便而产生的。

当你使用一个 CMS“无头”时，你就砍掉了做出租的部分。本质上，你的 CMS 管理你的内容，但是它提供了一个 API，其他东西可以使用它。

rea son Craft 的“实时预览”功能之所以有效，是因为 CMS 控制了整个编辑⟷预览循环。

现在，随着其他一些事情的发生，情况就不再是这样了。所以他们想出了一个聪明的解决办法。

## Tok 化预览

Pix el & Ton ic 的优秀人员提出的解决方案是自动保存的参赛作品草稿和发送到正在预览的网页的`token`的组合。

[![Nyc Token](img/a63bcb054906b882bae4191f5b544461.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HXHKTIu8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x573_crop_center-center_100_line/nyc-token.jpg)

当您点击**预览**时，大致会发生以下情况:

1.  您正在编辑的条目的草稿已保存
2.  为草稿生成令牌，并将关于草稿条目元素信息保存到数据库中
3.  无论网页是否作为一个`token` URL 参数出现，令牌都会被发送
4.  然后，网页*向*发回带有任何 API 请求的相同令牌

看起来大概是这样的:

[![Craft preview token flowchart](img/62463722bbdb5c024194f1012574b7bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AqDxuAFH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x728_crop_center-center_100_line/craft-preview-token-flowchart.png)

那么，为什么所有这些象征性的东西毫无意义呢？请记住，我们正在预览正在编辑的条目的自动保存草稿。

这样做是因为 con tent edi tor 和 con tent ren der 不再共享任何状态，所以保存的草稿就是那个状态。

Craft 使用该令牌将预览 web 请求链接到自动保存的条目草稿。

<aside>As you’re edit­ing con­tent with pre­view open, the draft entry is being reg­u­lar­ly saved</aside>

当 URL 参数中有一个`token`的请求进入 Craft 时，大致会发生以下情况:

1.  Craft 在数据库表`tokens`中查找与`token`相关的路线信息
2.  在无头预览的情况下，调用`Preview`控制器的`actionPreview()`方法
3.  然后，正在预览的自动保存的草稿元素被添加到`placeholder`元素列表中
4.  当匹配任何`placeholder`元素的[元素查询](https://docs.craftcms.com/v3/dev/element-queries/)完成时，它们被交换进来

这就是导致一切“正常工作”的原因。查看`ElementQuery`方法 [_ placeholderCondition()](https://github.com/craftcms/cms/blob/develop/src/elements/db/ElementQuery.php#L1870) 和 [_ createElement()](https://github.com/craftcms/cms/blob/develop/src/elements/db/ElementQuery.php#L2693) 。

由于令牌被传递到正在被预查看的网页，如果它被传递回从 Craft 检索数据的 API，占位符元素就会被模仿。

就像魔法一样。

## 使其如此

使用 Vue.js 的 [Live pre view 帖子详细描述了在您的终端上添加支持需要做的事情。](https://craftcms.stackexchange.com/questions/22045/live-preview-using-vue-js/31039#31039)

[![Star trek make it so picard](img/68b983ead8bd92482324a963ab39d203.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jjdJQraZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/star-trek-make-it-so-picard.jpg)

本质上，它归结为只是提取`token` URL 参数，并将其发送回 Craft CMS API 端点，无论它是 [Ele ment API](https://github.com/craftcms/element-api) 、[Craft QL plug g in](https://github.com/markhuot/craftql)，还是您可能正在使用的任何其他东西。

[![Craft cms token lifecycle](img/3bdab0b98bff84bd8d69b102789df7e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QcV8b5u---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x735_crop_center-center_100_line/craft-cms-token-lifecycle.png)

以下是布兰·唐·凯利发布的一些 JavaScript】

```
 // Get the preview token from the URL
let m = document.location.href.match(/\btoken=([^&]+)/);
let token = m ? m[1] : '';

// Then forward that on whenever you are sending an API request
let url = `...?token=${token}`;
// ... 
```

事实上就是这样。提取令牌 URL 参数，并将其与 API 调用一起发送回去。

<aside>If you send Craft back the token it sent you, it’ll take care of the rest</aside>

`x-craft-preview` URL 参数&请求标题只是一种你可以将请求定义为工艺预览的方式，因为你也可能将`token` URL 参数用于其他事情。

如果您需要将其更改为不同于默认值`token`的值，有一个[令牌参数](https://docs.craftcms.com/v3/config/config-settings.html#tokenparam)通用配置设置。

<aside>This exact same tech­nique is also used for Share links as well!</aside>

如果你担心预览[不记得跨域](https://github.com/craftcms/cms/issues/4410)的滚动位置，请查看 Clive 的[scroll memones 6 . js](https://gist.github.com/narration-sd/a10c5855cb0c06d562d7eb290b3251d3)要点。

Hap py 无头预览！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计