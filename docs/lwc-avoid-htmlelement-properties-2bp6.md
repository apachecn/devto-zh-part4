# LWC–避免 html 元素属性

> 原文：<https://dev.to/brettmn/lwc-avoid-htmlelement-properties-2bp6>

[https://www.youtube.com/embed/tIIMbzUlK0w](https://www.youtube.com/embed/tIIMbzUlK0w)

你好，我是布雷特和 WIPDeveloper.com。上一次，我们谈到了在我们的 Lightning web 组件中使用它作为一个属性，以及当启用 Lightning 调试模式时它是如何导致错误的。在 Twitter 上询问确实得到了 Kevin V .的回应，他在 Salesforce 工作，是 Lightning Web Components 背后的架构师之一。似乎所有的 HTMLElement 属性都被自动连接到 lightning 元素，或者 LWC 的 LightningElement。

因此，如果你在 MDN 文档或 Mozilla 文档上看到这个，你可以看到所有我们可能应该避免的属性名。这就是这里的列表。我在玩它。我做到了，我并不是说你应该这样做，其中一些实际上是可行的。和你一样，你使用可编辑的内容，并试图绑定到它，它不会抛出一个错误。但是它实际上并没有将值传递给模板。内部文本将值传递给模板。但是最好避免在 HTMLElement 上声明任何属性。现在这边，因为它实际上没有列在这里，应该就在`innerText`之前。它实际上是在元素上声明的。因此，你不仅应该避免所有这些，这可能是有意义的，你还应该避免在基本元素中声明的所有属性。这些也列在 MDN 网站上。所以如果你把它们列在你的网站上，或者你的 JavaScript 中，这将是一个相当大的列表。所以要避开 115 处房产。

现在，如果我取消其中一个的注释，比如，我不知道，`accessKey`然后应用它。我们将看到刷新页面。但是我确实得到了那个错误。因此，最好避免在 HTMLElement 和 Element one 上使用任何属性来处理 Lightning Web 组件。他们可能正在为林挺制定一项规则，以阻止您保存它。所以我会在下面列出一个清单。因此，如果你对此有问题，你可能会偶然发现这一点，并理解为什么。

## 属性回避

*   accessKey
*   属性
*   儿童人数
*   儿童
*   优等生名单
*   className
*   客户端高度
*   客户端左侧
*   客户端顶部
*   客户端宽度
*   当前样式
*   firstelemontchild
*   身份证明（identification）
*   innerHTML
*   lastelemontchild
*   本地端 Name
*   名字
*   namespaceURI
*   nextElementSibling
*   onfullscreenchange
*   onfullscreenerror
*   openOrClosedShadowRoot
*   outerHTML
*   前缀
*   前一个月兄弟姐妹
*   runtimeStyle
*   滚动高度
*   scrollLeft
*   scrollLeftMax
*   scrollTop
*   scrollTopMax
*   滚动宽度
*   暗影根
*   狭槽
*   制表符停止
*   tagName
*   内容可编辑
*   上下文菜单
*   资料组
*   目录
*   隐藏的
*   内部文本
*   isContentEditable
*   语言
*   目前
*   偏高
*   偏左
*   父代
*   抵消
*   偏移距离
*   奥纳博特
*   onanimationcancel
*   onanimationend
*   onanimationiteration
*   onauxclick
*   onblur
*   oncancel
*   在线播放
*   在线播放
*   昂哥
*   onclick
*   无糖的
*   oncontextmenu
*   oncopy
*   oncuechange
*   oncut
*   ondblclick(点击鼠标)
*   老化变化
*   统一的
*   不良事件
*   专注
*   ongotpointercapture
*   oninput
*   on 无效
*   onkeydown
*   onkeypress
*   onkeyup
*   装载
*   onloadeddata
*   onloadedmetadata
*   onloadend
*   onloadstart
*   onlostpointercapture
*   onmousedown
*   onmouseenter
*   onmouseleave
*   onmousemove
*   onmouseout
*   onmouseover
*   是 mouseup
*   粘贴时
*   暂停
*   monplay
*   onpointercancel
*   onpointerdown
*   onpointerenter
*   onpointeleave
*   onpointermove
*   onpointerout
*   onpointerover
*   onpointerup
*   onreset
*   onresize
*   onscroll
*   onselect
*   onselectionchange
*   onselectstart
*   昂松宾
*   ontouchcancel
*   ontouchstart
*   ontransitioncancel
*   ontransitionend
*   车轮上
*   外部文本
*   风格
*   tab 键索引
*   标题

## 链接

*   [MDN 上的元素](https://developer.mozilla.org/en-US/docs/Web/API/Element)
*   [HTMLElement on MDN](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement#Properties)

> [@diervo](https://twitter.com/diervo?ref_src=twsrc%5Etfw) 是对的:`id`所有 HTMLElement 属性都自动挂接到 LightningElement。见[https://t.co/4JOB4pVv8f](https://t.co/4JOB4pVv8f)。修复方法是使用不与 HTMLElement 上的属性冲突的变量名。
> 
> —凯文·文基特斯瓦兰(@ kevinv 11n)[2019 年 8 月 20 日](https://twitter.com/KevinV11n/status/1163683197617467393?ref_src=twsrc%5Etfw)

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[WIPDeveloper.com](https://wipdeveloper.com/lwc-avoid-htmlelement-properties/)最先出现在[上【LWC-避免 html 元素属性](https://wipdeveloper.com)。