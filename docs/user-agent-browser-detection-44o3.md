# 用户代理(浏览器)检测

> 原文：<https://dev.to/amarok24/user-agent-browser-detection-44o3>

这是我通过 JavaScript 用于浏览器环境嗅探的一小段代码，代码灵感来自 2019 年 7 月的 Vue.js 源代码。

```
var inBrowser = typeof window !== "undefined";
var browser_UA = inBrowser && window.navigator.userAgent.toLowerCase();
var browser_isIE = browser_UA && /msie|trident/.test(browser_UA);
var browser_isIE11 = browser_isIE && browser_UA.indexOf('11.0') > 0;
var browser_isEdge = browser_UA && browser_UA.indexOf('edge/') > 0;
var browser_isAndroid = browser_UA && browser_UA.indexOf('android') > 0;
var browser_isIOS = browser_UA && /iphone|ipad|ipod|ios/.test(browser_UA);
var browser_isChrome = browser_UA && /chrome\/\d+/.test(browser_UA) && !browser_isEdge;
var browser_isGoogleChrome = browser_isChrome && !!window.googleapis;
var browser_isChromeDerivate = browser_isChrome && !browser_isGoogleChrome;
var browser_isFirefox = browser_UA && !!browser_UA.indexOf("firefox/");

//ANOTHER:
var browser_FFversion = browser_isFirefox && browser_UA.match(/firefox\/(\d+)/)[1];
// FF version then stored in array[1] 
```

Enter fullscreen mode Exit fullscreen mode