# 基于用户首选项的区域设置

> 原文：<https://dev.to/qdequippe/locale-based-on-the-user-s-preferences-2gp8>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [基于用户偏好的区域设置](https://stackoverflow.com/questions/57995513/locale-based-on-the-users-preferences)

Sep 18 '19 Comments: 1 Answers: 1[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)1![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/57995513/locale-based-on-the-users-preferences) </header>

我尝试根据用户设置(即用户实体中的“language”属性)设置区域设置，但在第一次请求(用户已验证)时失败，因为 LocaleListener(以及 TranslatorListener)在 FirewallListener 之前被调用。

我尝试了这个例子[在用户会话](https://symfony.com/doc/current/session/locale_sticky_session.html#setting-the-locale-based-on-the-user-s-preferences)期间使区域设置“粘滞”,但是首先…

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57995513/locale-based-on-the-users-preferences)</button>