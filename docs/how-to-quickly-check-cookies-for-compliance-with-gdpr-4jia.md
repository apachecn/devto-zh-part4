# 如何快速检查 cookies 是否符合 GDPR🍪

> 原文：<https://dev.to/yolaris/how-to-quickly-check-cookies-for-compliance-with-gdpr-4jia>

在建立了我的小博客后，我已经有了一个基本的解决方案来使 cookies GDPR 兼容，但是对我来说太贵了。我没有那么高兴，所以我决定探索 GDPR 的要求。

一般来说，如果您的网站被欧盟消费者访问，即使您不收集任何个人数据，您也必须遵守 GDPR。由于第三方可以通过 cookie 跟踪网站访问者，所以你应该时刻关注网站加载时设置的 cookie。

## GDPR 兼容的饼干🥮

如果您在获得事先同意后设置 cookies，则您遵守 GDPR。同意横幅应该阻止 cookies，直到用户单击允许按钮。

如果您在没有事先获得同意的情况下根本不设置任何 cookie 或设置绝对必要的 cookie，您也遵守了 GDPR。
欧盟委员会[向](https://ec.europa.eu/ipg/basics/legal/cookies/index_en.htm)提供了此类绝对必要的饼干的例子:

*   会话期间用户输入的 cookies
*   会话期间的身份验证 cookies
*   以用户为中心的安全 cookies，用于检测身份验证滥用，并在有限的持续时间内链接到用户明确请求的功能
*   多媒体内容播放器会话 cookies，在会话期间
*   在会话期间，负载平衡会话 cookies
*   用户界面定制 cookies，用于浏览器会话或几个小时

## 谷歌分析怎么样

任何统计、广告或类似的 cookies 都不是绝对必要的，没有事先获得同意不能设置。如果你使用 Google Analytics、Google Adsense 或 DoubleClick，你应该得到用户的同意。顺便说一下，谷歌[就此警告](https://www.google.com/about/company/user-consent-policy/)。

## 如何快速查看你的 cookies

要检查网站 cookies 是否符合 GDPR 标准，您可以使用 [2GDPR](https://2gdpr.com)
扫描大约需要一分钟。
如果发现违规，将需要更长时间来分析结果。