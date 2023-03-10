# 当你打算放弃 IE11 🗑️的时候，你会期待什么

> 原文：<https://dev.to/samthor/what-to-expect-when-you-re-expecting-to-drop-ie11-ifg>

所以你已经决定放弃对 IE11 的支持，转而只使用 evergreen 浏览器(IE11 在全球只占 2%)。太好了！🌲

考虑到这一点，这里有一个庞大的功能列表，您应该在今天(今天是 2019 年年中)、安全地、*在没有*poly fill 或功能检测的情况下使用。📃

当然，在我们开始之前，总会有旧的浏览器。而且，平心而论，新兴市场的浏览器更复杂:像 UC、KaiOS(基于较老的 Firefox)和 Opera Mini。在这些情况下，我建议不提供任何 JS(如果可能的话)，或者鼓励用户升级。🤷

我们走吧！⬇️

### DOM

*   根据分辨率选择图片网址[📽️](https://www.youtube.com/watch?v=SyVKRnusyqM) (通过`<img srcset>`和`<picture>`)

*   帧[可以从](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/9121088/) a `Blob`加载

*   用 [`<fieldset disabled>`](https://dev.to/chromiumdev/disable-a-html-form-while-in-flight-using-fieldset-61b) 禁用表单元素，对正在进行的表单有用

*   HTML 输入类型`color` [和各种日期/时间选项](https://robertnyman.com/html5/forms/input-types.html)

*   HTML 模板和`<template>`元素(这也在 JS 中，但是您可以在您的页面中指定它们)

*   `<meter>`元素([随`<progress>`](https://peter.sh/examples/?/html/meter-progress.html) )

### JavaScript 语言

*   **ES 模块，通过`<script type="module">`和`import`/`export`/**🎉

*   模板文字(带反斜杠)

*   像`class Foo { constructor() { ... } }`这样的类

*   函数！箭头功能，剩余参数，`async`功能允许`await`，发生器允许`yield`

### JavaScript 库

*   `Promise`[`fetch`](https://developers.google.com/web/fundamentals/primers/async-functions)(不再需要`XMLHttpRequest`🚫)

    *   ...XHR 的`responseType`现在也可以[安全地设置为“JSON”](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/responseType)，但是为什么要麻烦呢？🤷
*   `Array`上的方法:`find`，`includes`；在`String`上:`includes`、`padStart`和`padEnd`

*   `Proxy`物体，允许有趣的[接近](https://exploringjs.com/es6/ch_proxies.html)

*   `Object` : `entries`，`values`上的方法，用于迭代(如`Object.keys`

*   `URL`对象(用于检查查询参数和处理 URL)

*   `currentScript`属性([“我是什么文件”](https://developer.mozilla.org/en-US/docs/Web/API/Document/currentScript))

*   你可以安全地派遣一个`new CustomEvent('....')`而不是用奇怪的初始化器处理

*   `Symbol`和朋友

### JavaScript+DOM

*   [更好的 HTML 便利方法](https://dev.to/chromiumdev/beyond-appendchild-better-convenience-methods-for-html-55n4)

*   第三个参数为`addEventListener`，允许你设置`{once: true}`和[其他选项](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)

*   `IntersectionObserver`，允许你告诉[DOM 节点是否可见](https://developers.google.com/web/updates/2016/04/intersectionobserver)

*   `navigator.sendBeacon`方法，即使页面关闭也能发送帖子信息

*   找到与`closest`最匹配的[元素](https://dev.to/samthor/matching-elements-with-selectors-in-js-4991)

*   `classList.toggle`的第二个参数，允许你通过参数(也是链接的`.relList`属性)设置或删除一个类

*   画布混合模式([这是`.globalCompositeOperation`属性](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation))

*   通过`CSS.supports`确定一个 CSS 特性是否被支持(但这只帮助*未来的*特性)

### 全新 API

*   [**服务人员**](https://jakearchibald.github.io/isserviceworkerready/) 🥳

*   [网络组装](https://www.youtube.com/watch?v=iPtMiqutNT4)👩‍💻

*   游戏手柄 API🎮

*   网络音频 API📣

*   [指针锁 API](https://mdn.github.io/dom-examples/pointer-lock/) :对 HTML 游戏和丰富体验有用🐁🔒

*   约束验证 API(改进的表单验证)📏

*   [WebRTC](https://webrtc.org/) 📽️

*   `getUserMedia`访问视频、音频流🙏

### CSS

*   **网格🎉**

*   CSS 变量，如`--foo: blue;`，与`color: var(--foo)`一起使用

*   粘性位置

*   CSS 滤镜，允许视觉效果，如反转，阴影和色调变化

*   **图像`object-fit`** (Edge 仅在`<img>`上支持)，允许您使图像包含或覆盖其内容，而不是*拉伸*

*   改进了指针或鼠标访问的媒体查询<small>有趣的事实:这是我在 Chrome 上写的第一个演示。</small>

*   新的 [CSS 光标](https://developer.mozilla.org/en-US/docs/Web/CSS/cursor)'抓取'，'放大'，'缩小'

*   `::placeholder`伪元素，用于样式化`<input>`中的占位符文本

*   使用`initial`或`unset`T2 作为 CSS 值

*   `vmax`单位，是宽度[或高度](https://css-tricks.com/fun-viewport-units/)中较大者的百分比

*   伴随着 JS 方法，CSS `@supports` [at-rule](https://developer.mozilla.org/en-US/docs/Web/CSS/@supports)

*   只读和读写伪类选择器(`:read-write`似乎[更有用](https://developer.mozilla.org/en-US/docs/Web/CSS/:read-write)

*   [在文本上描边并填充](https://webkit.org/blog/85/introducing-text-stroke/)

    *   ...虽然所有的常青树都支持，但是你需要包括`-webkit-`前缀:是的，*甚至对于 Edge 和 Firefox*
*   IE11 中的危险错误不再是问题:

    *   你现在可以安全地将`calc(...)` [放入 CSS 动画](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/105834/)中
    *   CSS `display: flex`有[各种各样的问题](https://github.com/philipwalton/flexbugs)

# 唷！

你已经走了这么远了！恭喜你！🎉

如果我错过了什么，或者这些特性的好资源，请在下面告诉我。

Seventeen