# 不要被跟踪⚙️调整了火狐的配置

> 原文：<https://dev.to/pierre/don-t-be-tracked-tweak-the-firefox-configurations-9e4>

Firefox 的优势在于它是目前最容易调整和定制的浏览器。

如果你关心你的隐私，下面是你应该对你的 Firefox 桌面做的 15 个配置更改，这样你就不会再被跟踪了。

首先，在火狐的地址栏输入`about:config`，然后按照上面的步骤⬇

### 1。搜索`media.peerconnection.enabled`并设置为`false`

当设置为 TRUE 时，它允许使用 WebRTC 技术进行实时通信，如视频聊天和音频。

可悲的是，有一个缺陷是通过浏览器 STUN 请求泄漏你的真正唯一的 IP 地址，即使你使用的是 VPN 服务。这就是为什么禁用这个选项是一个明智的决定，不像你使用一个需要 WebRTC 的网站。

### [2](#2-set-raw-privacyresistfingerprinting-endraw-to-raw-true-endraw-which-makes-firefox-more-resistant-to-browser-fingerprinting-it-prevents-sharing-technical-information-about-your-web-browser-ip-operating-system-details-etc)。将`privacy.resistFingerprinting`设置为`true`，这使得 Firefox 更能抵抗浏览器指纹识别。它会阻止共享有关您的网络浏览器、IP、操作系统详细信息等技术信息。

### 3。将`geo.enabled`设置为`false`以禁用地理位置跟踪。

正如在 [Mozilla 网站](https://support.mozilla.org/en-US/kb/does-firefox-share-my-location-websites#w_what-information-is-being-sent-and-to-whom-how-is-my-privacy-protected)上解释的那样，当用户接受地理定位时，Firefox 使用谷歌定位服务，通过向谷歌发送:

*   您计算机的 IP 地址。
*   关于附近无线接入点的信息。
*   由 Google 分配的随机客户端标识符，每两周到期一次。

### [4](#4-set-raw-networkcookielifetimepolicy-endraw-to-raw-2-endraw-)。将`network.cookie.lifetimePolicy`设置为`2`

该值应该是`2`，它只为当前会话保存网站的 cookies。它们将在会话结束时自动删除。

其他可能的值有:

`0` =正常接受 cookie
`1`=每个 cookie 提示
`2` =仅接受当前会话
`3` =接受 N 天

### 5。将`privacy.firstparty.isolate`设置为`true`，这将 cookies 隔离到第一方域，并防止它们跨多个域使用。

### [6](#6-set-raw-networkcookiecookiebehavior-endraw-to-raw-1-endraw-)。将`network.cookie.cookieBehavior`设置为`1`

这里最好的值是`1`，它只接受来自原始站点的 cookie(阻止第三方 cookie)。

其他可能的值有:

`0` =默认情况下接受所有 Cookie
`1`=仅接受来自原始站点的 Cookie(阻止第三方 Cookie)
`2`=默认情况下阻止所有 Cookie
`3`=阻止来自未访问站点的 Cookie
`4`=新的 Cookie Jar 策略(阻止跟踪器访问存储)

### [7](#7-set-raw-networkdnsdisableprefetch-endraw-to-raw-true-endraw-)。将`network.dns.disablePrefetch`设置为`true`

尽管禁用域名解析可能会略微降低页面加载速度，但它将保护您免受隐私和安全威胁。

### 8。将`webgl.disabled`设置为`true`将防止您的设备被指纹识别，以及使用 WebGL 的任何潜在安全风险。

### [9](#9-set-raw-domeventclipboardeventsenabled-endraw-to-raw-false-endraw-)。将`dom.event.clipboardevents.enabled`设置为`false`

停用此选项后，网站将不会知道您是否从他们的网页上拷贝/粘贴了某些内容，也不会知道页面的哪一部分被选中了。

### [10](#10-set-raw-dombatteryenabled-endraw-to-raw-false-endraw-)。将`dom.battery.enabled`设置为`false`

它可以防止网站知道你使用的是电池还是交流电源。

### [11](#11-set-raw-browserurlbarspeculativeconnectenable-endraw-to-raw-false-endraw-)。将`browser.urlbar.speculativeConnect.enable`设置为`false`

它禁止预加载自动完成的网址。Firefox 预装了 URL，当用户在地址栏中输入时会自动完成。

### [12](#12-set-raw-browsercacheofflineenable-endraw-to-raw-false-endraw-if-you-dont-use-the-offline-mode)。如果不使用离线模式，将`browser.cache.offline.enable`设置为`false`。

如果是真的，它允许网站在你离线时在你的电脑上存储数据。

### 13。将`media.navigator.enabled`设置为`false`以防止网站知道您的设备上是否有麦克风或摄像机。

### 14。将`network.prefetch-next`设置为`false`可以防止网页被 Firefox 预取。

Mozilla 已经部署了这个选项来提高页面加载速度。但是，它会给隐私带来风险。这就是为什么禁用这个选项更明智。

### [15](#15-set-raw-networkttrmode-endraw-to-raw-2-endraw-)。将`network.ttr.mode`设置为`2`

Mozilla 计划在默认情况下使用 HTTPS 域名系统(DoH ),然而，对我们大多数人来说，它在默认情况下是不启用的。

通过 HTTPS 协议执行远程域名系统(DNS)解析可以防止您的 ISP 和网络管理员知道您访问的 URL。

`network.ttr.mode`的其他可能值有:

`0` =标准 Firefox 安装中的默认值(当前为`5`，这意味着 DoH 被禁用)
`1` = DoH 被启用，但 Firefox 会根据哪个返回更快的查询响应来选择是使用 DoH 还是常规 DNS
`2`= DoH 被启用，常规 DNS 作为备份
`3` = DoH 被启用，常规 DNS 被禁用
`5` = DoH 被禁用

仅此而已！😌

希望你现在可以享受你的隐私权了。

我很高兴听到你的任何反馈或建议。