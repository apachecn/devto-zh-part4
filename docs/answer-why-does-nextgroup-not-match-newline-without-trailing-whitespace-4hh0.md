# 回答:为什么 nextgroup 不匹配没有尾随空格的换行符？

> 原文：<https://dev.to/_alief_aziz/answer-why-does-nextgroup-not-match-newline-without-trailing-whitespace-4hh0>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答 re:为什么 nextgroup 不匹配没有尾随空格的换行符？](https://stackoverflow.com/questions/14426845/why-does-nextgroup-not-match-newline-without-trailing-whitespace/18148503#18148503)

Aug 9 '13[![](img/e3f0373ec76330150a340eacd410b600.png)2![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/14426845/why-does-nextgroup-not-match-newline-without-trailing-whitespace/18148503#18148503) </header>

如果一个匹配在`\n`之前结束，那么`nextgroup`参数将被忽略，除非`skipnl`也被指定，即使`nextgroup`中的一个组以`\n`开始匹配。

在给定的例子中，由于第一场`Foo`比赛正好在`\n`和`Foo`比赛之前结束…

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/14426845/why-does-nextgroup-not-match-newline-without-trailing-whitespace/18148503#18148503)</button>