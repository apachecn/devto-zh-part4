# JS&Friends Conf:掌握暗影 DOM - Martine Dowden

> 原文：<https://dev.to/aromig/js-friends-conf-mastering-shadow-dom-martine-dowden-dn8>

## 掌握暗影 DOM - [马丁·道登](https://twitter.com/martine_dowden)

[![Martine Dowden](img/1155ee9e72d246813c9b409b064d13ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IrHKuydw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/25bm2tjoelbz6ine2yun.jpg)

Martine 向我们展示了如何只用 HTML 和普通的 JavaScript 创建 web 组件——没错，没有框架！

我们了解了什么是 Web 组件和定制元素，以及 Shadow DOM 如何为我们提供封装可重用组件的能力。在回顾了 JavaScript 的基本 DOM 操作之后，她向我们展示了如何通过带回`<blink>`标签来使用这些技术创建 web 组件！

**定义我们的标签**

JavaScript

```
// blink.js
const Blink = (function() {
  "use strict";

  class Component extends HTMLElement {
    constructor() {
      super();
      // Create shadow root
      const shadow = this.attachShadow({
        mode: "open"
      });

      // Wrap text content with a span tag
      const wrapper = document.createElement("span");

      // Make a slot for text
      const slot = document.createElement("slot");
      slot.setAttribute("name", "content");
      wrapper.appendChild(slot);

      // CSS animation styles
      const style = document.createElement("style");
      style.textContent = `
        @keyframes blink {
          0%   { visibility: hidden; }
          50%  { visibility: hidden; }
          100% { visibility: visible; }
        }
        /* :host selects shadow root host */
        :host { animation: 1s linear infinite blink; }
      `;

      // Append
      shadow.appendChild(wrapper);
      wrapper.appendChild(style);
    }
  }
  customElements.define("wc-blink", Component); // can't use <blink> - it's still reserved
})();

export { Blink }; 
```

**投入使用**

HTML

```
<script>
  import { Blink } from "./blink.js";
</script>

<!-- prettier-ignore -->
<wc-blink>
  <h1 slot="content">
    Look Ma, I'm blinking!
  </h1>
</wc-blink> 
```

在这个会议中，我学到了很多关于如何使用 Shadow DOM 来制作定制元素以及这样做的好处和局限性。我带着这样一种理解离开了，即如果需要的话，在没有框架的情况下创建基于组件的站点/应用程序是可能的。尽管一个框架的额外特性肯定比独立的自由更重要，但是知道有选择是很好的。

[←回主 JS &好友文章](https://dev.to/aromig/my-first-dev-conference-javascript-friends-fh7)