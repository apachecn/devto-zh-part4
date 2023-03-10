# chromedriver 中的 cdc 变量是什么？

> 原文：<https://dev.to/tonetheman/what-is-the-cdc-variable-in-chromedriver-553p>

我在 reddit 上看到这个帖子，我想知道 WTF 是 chromedriver 中的 cdc 变量

[https://www . Reddit . com/r/selenium/comments/cfjydg/selenium _ chrom driver _ replaced _ CDC _ variable/](https://www.reddit.com/r/selenium/comments/cfjydg/selenium_chromdriver_replaced_cdc_variable/)

那到底是什么？你为什么会在乎？

当你使用 chromedriver 时，它是一个显示在文档中的变量。用一件细微的事情...如果您只是运行 driver.get，并且不与 Selenium 中的页面进行交互，那么这个变量就不会显示出来...

知道它的网站可以检测到它们是从自动浏览器被调用/显示的。

如果您在 javascript 中查看文档[" $ CDC _ asdjflasutopfhvcZLmcfl _ "]就可以看到它。您可以通过将这段代码添加到 Python 中的 Selenium 脚本来验证这一点。

```
print(driver.execute_script("return document['$cdc_asdjflasutopfhvcZLmcfl_']")) 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的是 chromedriver(或者至少从 chromedriver 75 开始)，这个总是会返回一些东西。

那个变量从何而来？它是嵌入在 chromedriver 中的代码。你可以自己在 chromium 源代码中找到。[https://github . com/chrome/chromium/tree/master/chrome/test/chrome driver](https://github.com/chromium/chromium/tree/master/chrome/test/chromedriver)

如果您想更改它以便您的 Selenium 脚本不被检测到，该怎么办？不那么容易的方法是自己编译(但是作为一个书呆子你会得到分数)。

要改变这个变量，更简单的方法是编辑 chromedriver 可执行文件。请参阅下面链接，了解一种合理的方法。请注意，当您更改可执行文件时，它会触发 Win10 病毒扫描，这是您在入侵后第一次运行它。

我在 Windows 10 上用 vim 用过这个，效果不错。

[https://stack overflow . com/questions/33225947/can-a-website-detect-when-you-use-selenium-with-chrome driver](https://stackoverflow.com/questions/33225947/can-a-website-detect-when-you-are-using-selenium-with-chromedriver)