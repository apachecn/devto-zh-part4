# 您将如何检测 iframe 中内容的高度？

> 原文：<https://dev.to/ben/how-would-you-go-about-detecting-the-height-of-content-within-an-iframe-14d8>

假设您有一个`iframe`，并且您想在内容加载后检测其`window`的高度，您将如何做呢？

这种方法似乎令人满意，但我觉得这是一个可能存在边缘情况和陷阱的领域，所以我对不同的方法持开放态度。

<header>![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [answer re: make iframe height dynamic based on content inside- JQUERY/Javascript](https://stackoverflow.com/questions/9162933/make-iframe-height-dynamic-based-on-content-inside-jquery-javascript/9163087#9163087) Feb 6 '12[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)236![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/9162933/make-iframe-height-dynamic-based-on-content-inside-jquery-javascript/9163087#9163087) </header>

您可以使用`contentWindow.document.body.scrollHeight`来检索`IFRAME`内容的高度

加载`IFRAME`后，您可以通过以下操作更改高度:

```
<script type="text/javascript">
  function iframeLoaded() {
      var iFrameID = document.getElementById('idIframe');
      if(iFrameID) {
            // here you can make the height, I delete it first,
```

…[Open Full Answer](https://stackoverflow.com/questions/9162933/make-iframe-height-dynamic-based-on-content-inside-jquery-javascript/9163087#9163087)