# 复古计划🐡在 W3C TPAC 会议上

> 原文：<https://dev.to/tomayac/project-fugu-at-w3c-tpac-2ngi>

本周，我参加了我现在的第三次<abbr title="World Wide Web Consortium">W3C</abbr>T2TPAC。继美国加州伯林盖姆 [TPAC 2017](https://www.w3.org/2017/11/TPAC/Overview.html) 和法国里昂 [TPAC 2018](https://www.w3.org/2018/10/TPAC/) 之后， [TPAC 2019](https://www.w3.org/2019/09/TPAC/Overview.html) 在日本福冈举行。第一次，我觉得我可以*有意义地*做出贡献，并且至少对 W3C 底层机制有了一个*基线理解*。和每年一样，TPAC 议程安排得很满，重叠是不可避免的。以下是我有时间参加的会议的详细记录。

#### 第 1 天

周一，我参加了[服务工作者工作组](https://www.w3.org/sw/) (WG)会议。这次的[议程](https://github.com/w3c/ServiceWorker/issues/1460#issue-482168365)是实现者更新、新提议和许多特殊案例讨论的混合。我知道[杰克·阿奇博尔德](https://twitter.com/jaffathecake)正在写一篇总结文章，所以我让他来总结这一天。如果你感兴趣的话，可以得到原始的会议记录。

#### 第二天

周二，我以观察员身份参加了 [Web 应用安全工作组](https://www.w3.org/2011/webappsec/)会议。我对这个工作组最感兴趣，因为[议程](https://github.com/w3c/webappsec/blob/master/meetings/2019/2019-09-TPAC-agenda.md)承诺了一些有趣的提议，比如苹果的 [`/.well-known/change-password`](https://wicg.github.io/change-password-url/index.html) 得到了普遍同意。一些有趣的讨论也再次引发了围绕苹果 [`isLoggedIn()`](https://lists.w3.org/Archives/Public/public-webappsec/2019Sep/0004.html) API 的提议。我想起了为什么在网络上，我们不能通过一个利用 <abbr title="HTTP Strict Transport Security">HSTS</abbr> 进行跟踪的攻击媒介来获得好东西。幸运的是，有[浏览器缓解](https://webkit.org/blog/8146/protecting-against-hsts-abuse/)来防止这种情况。[会议纪要](https://github.com/w3c/webappsec/blob/master/meetings/2019/2019-09-TPAC-minutes.md)覆盖全天。

#### 第三天

周三是与 [59(！)分组会议](https://w3c.github.io/tpac-breakouts/sessions.html)。除了有时乏味的工作组会议，我发现分组会议通常更有趣，是学习新事物的机会。

##### 分组讨论 JS 内置模块

我参加的第一个分组会议是关于 [JS 内置模块](https://w3c.github.io/tpac-breakouts/sessions.html#jsbuiltin)，这是苹果 <abbr title="Technical Committee 39">TC39</abbr> 关于 [JavaScript 内置库](https://github.com/tc39/proposal-javascript-standard-library)的提议。[会议纪要](https://www.w3.org/2019/09/18-jsbuiltin-minutes.html)可用，总的来说，围绕名称空间和如何管理内置模块有很多讨论和分歧。

##### 分组会议新模块类型:JSON、CSS 和 HTML

接下来的会议是关于 JSON、CSS 和 HTML 的新模块类型的[。作为](https://w3c.github.io/tpac-breakouts/sessions.html#new-modules) [`<dark-mode-toggle>`](https://github.com/GoogleChromeLabs/dark-mode-toggle) 的开发者，我完全赞成把笨拙的 [`innerHTML`全部去掉！！！1!程序员目前遵循普通 JS 定制元素的方法。如果你同样感兴趣，订阅 Web 组件工作组报告中的](https://github.com/GoogleChromeLabs/dark-mode-toggle/blob/bf737bed7a7d3ba5086585a94578ed814500bb6c/src/dark-mode-toggle.mjs#L75-L249) [CSS 模块问题](https://github.com/w3c/webcomponents/issues/759)和 [HTML 模块问题](https://github.com/w3c/webcomponents/issues/645)。讨论主要围绕`@imports`如何工作以及如何传达导入类型以避免安全问题的细节，例如遵循`<link rel="preload">`方式。[会议纪要](https://www.w3.org/2019/09/18-new-modules-minutes.html)有全部细节。

```
// Non-working example
import styles from 'styles.css' as stylesheet;
import settings from 'settings.json' as json; 
```

##### 分组会议迷你 App 标准化

由[中文网络兴趣小组](https://www.w3.org/2018/chinese-web-ig/)组织的[迷你应用标准化](https://w3c.github.io/tpac-breakouts/sessions.html#miniapp)会议对我来说非常有趣。为了准备在中国上海举办的[谷歌开发者日](https://events.google.cn/intl/zh-CN/developerdays2019/)，我在 TPAC 之前发表了演讲，我[查看了微信迷你程序](https://blog.tomayac.com/2019/08/15/a-quick-look-at-wechats-mini-programs/)并记录了开发者的体验以及他们离网络有多近和有多远。在 TPAC 的几天前，中国网络利益集团发布了一份记录他们想法的白皮书。各种迷你应用平台取得的成功值得我们充分尊重。然而，有各种各样的声音——包括来自<abbr title="Technical Advisory Group">标签</abbr>的声音——敦促各种利益相关者将他们的工作与在进步的 Web 应用领域所做的努力结合起来，例如围绕 [Web 应用清单](https://w3c.github.io/manifest/)而不是创建另一种类似清单的格式。阅读[完整会议记录](https://www.w3.org/2019/09/18-miniapp-minutes.html)了解所有详情。会议的成果之一是创建了我希望加入的 [MiniApps 生态系统社区组](https://www.w3.org/community/miniapps/)。

##### 一个更有能力的网络的分组会议——府谷项目

我与英特尔公司的安西·科斯泰宁和微软公司的约翰·詹森一起在 T4 组织了一次分组会议，主题是“府谷计划”下的更有能力的网络🐡。你可以在下面看到我们的幻灯片。在会议中，我们认为，为了保持与本地、混合或迷你应用程序的相关性，web 应用程序也需要访问一组可比较的 API。我们简要介绍了跨公司项目合作伙伴正在开发的 API，然后就为什么我们会看到浏览器可访问的 web 处于危险之中展开了公开讨论，尽管隐私、安全和兼容性方面存在着众所周知的挑战，但我们现在不推进它。可以关注优秀(！)[会议纪要](https://www.w3.org/2019/09/18-capable-web-minutes.html)，Anssi 提供。

[https://glitch.com/embed/#!/embed/project-fugu-at-w3c-tpac?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/project-fugu-at-w3c-tpac?previewSize=100&path=index.html)

#### 第 4 天和第 5 天

周四和周五专门用于[设备和传感器工作组](https://www.w3.org/das/)。[的日程](https://github.com/w3c/devicesensors-wg/issues/24)并不太满，但仍然让我们忙碌了一天半。我们几乎从一开始就讨论了权限以及应该如何处理它们。权限是府谷项目中的一个大话题🐡我很高兴标签中正在进行改善这种情况的工作，包括围绕[权限 API](https://developer.mozilla.org/en-US/docs/Web/API/Permissions_API) 的工作，不幸的是，它没有得到普遍支持，导致一些 API 具有获取权限的静态方法，另一些 API 在第一次使用尝试时请求权限，还有一些 API 与权限 API 集成不一致。对于[地理位置传感器 API](https://w3c.github.io/geolocation-sensor/) ，我们同意尝试将前台跟踪的表达配置改造到[地理位置 API](https://w3c.github.io/geolocation-api/) 规范中，而不是在地理位置传感器中进行，这将提高供应商的采用率。对于地理围栏和后台地理位置跟踪，我们决定分别探索[通知触发器](https://github.com/beverloo/notification-triggers)和[唤醒锁](https://w3c.github.io/wake-lock/)，当地理位置传感器的工作开始时，这两者都不是选项。

[工作组特邀专家 Maryam Mehrnezhad](https://sites.google.com/view/maryammjd/home) 的研究重点是隐私和安全，他向我们介绍并与我们讨论了传感器在这两个领域的潜在影响，以及精度限制或频率限制等缓解措施是否有效。[会议记录](https://www.w3.org/2019/09/19-dap-minutes.html#x18)很好地记录了对话。

最后，我们[改变了唤醒锁 API](https://github.com/w3c/wake-lock/issues/226#issuecomment-533032056) 的表面，希望这是最后一次。从开发人员体验的角度来看，之前的更改感觉不太好，所以最好趁 API 还在标记后面的时候更改它，而不是永远后悔。我想我确实为实施者[里朱布拉塔·巴米克](https://twitter.com/rijubrata)和[拉斐尔·库博·达·科斯塔](https://github.com/rakuco)感到遗憾…🙇

```
partial interface Navigator {
  [SameObject] readonly attribute WakeLock wakeLock;
};

partial interface WorkerNavigator {
  [SameObject] readonly attribute WakeLock wakeLock;
};

[Exposed=(Window,DedicatedWorker)]
interface WakeLock {
  Promise<unsigned long long> request(WakeLockType type);
  Promise<void> release(unsigned long long wakeLockID);
};

dictionary WakeLockEventInit {
  required unsigned long long wakeLockID;
};

[Exposed=(Window,DedicatedWorker)]
interface WakeLockEvent : Event {
  constructor(DOMString type, WakeLockEventInit init);
  readonly attribute unsigned long long wakeLockID;
}; 
```

作为一个总的主题，我们“强化”了许多 API，例如决定将地理定位与[功能策略](https://w3c.github.io/webappsec-feature-policy/)集成，现在需要为[电池状态 API](https://w3c.github.io/battery/) 建立安全连接。安西和[雷利·格兰特](https://twitter.com/reillyeon)两位主持人出色地记录了一天半的时间，第一天[和第二天](https://www.w3.org/2019/09/19-dap-minutes.html)的会议记录都在网上。

#### 结论

正如我在开头所写的，TPAC 开始慢慢感觉像是一个我可以做出一些有价值贡献的地方。[罗文·梅莱伍德](https://twitter.com/rowan_m)在[的推特](https://twitter.com/rowan_m/status/1173808373436862464)中这样写道:

> 我在[ [#W3Ctpac](https://twitter.com/hashtag/w3ctpac?src=hashtag_click) ]学到的最重要的事情是，如果你想改变网络，你需要说服的人少得惊人。W3C 的外表和它所有的语言令人生畏，但是在它的下面，你可以和其他人交谈。

对此[真理子小坂](https://twitter.com/kosamari)[恰如其分地回应](https://twitter.com/kosamari/status/1173811848518356993):

> [Y]]是的，但我们不要忘记，与这一小群人交谈通常伴随着非常、非常、非常的特权。[…]

为一家有钱参加 W3C 活动的公司工作，让人们飞遍世界，并让他们住在五星级会议酒店，这确实是一种巨大的特权。带着对网络的热爱和对奇妙的 TPAC 的美好回忆，我们不要忘记:网络受到来自多个角度的威胁，能够在标准机构中致力于捍卫它是少数人的特权。两者都不应该是这样。