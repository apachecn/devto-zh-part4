# 展望空间解放——一个坦佩姆斯基剧本

> 原文：<https://dev.to/fischgeek/outlook-space-liberation-a-tampermonkey-script-47jc>

我并不经常去 outlook.com，但当我去的时候，我无法告诉你 ad-block(在这种情况下是 Ghostery)无法屏蔽这些部分让我有多恼火。所以，我做了任何人都会做的事情，[我创建了一个 tampermonkey 脚本](https://gist.github.com/fischgeek/811512bd5ec5de929206ee19c45200d4)来帮我。我想我应该分享一下。希望它带给别人的快乐和带给我的一样多。

```
// ==UserScript==
// @name         Outlook Space Liberation
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  Gains that space back by annoying ads and upgrade prompts.
// @author       fischgeek
// @match        https://outlook.live.com/mail/*
// @grant        none
// @require      http://code.jquery.com/jquery-1.12.4.min.js
// ==/UserScript==

var $ = window.jQuery
$(document).ready(function() {
    setInterval(function(){
        $('[data-icon-name="OutlookLogo"]').parent().parent().parent().hide()
        $('[role="menu"]').prev().hide()
        $('span:contains("Upgrade to Office 365")').parent().parent().parent().hide()
    }, 500)
}) 
```

Enter fullscreen mode Exit fullscreen mode