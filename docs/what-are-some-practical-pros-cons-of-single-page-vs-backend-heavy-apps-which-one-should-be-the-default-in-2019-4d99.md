# 单页面与后端重度应用相比，有哪些优缺点？

> 原文：<https://dev.to/prahladyeri/what-are-some-practical-pros-cons-of-single-page-vs-backend-heavy-apps-which-one-should-be-the-default-in-2019-4d99>

目前，webdev 世界正处于一种变化之中。有人称之为“FE 方式”,它包括 angular 和 vue 等库的混合，用于基于浏览器的路由(使用哈希)和创建 MVC，加上 react、material-ui、prime-ng 等其他库，用于处理视图。后端只是一个 REST API，可以用你想要的任何语言免费实现。

另一方面,“BE 方式”涉及一些旧的(但仍然可靠和稳定的)库，如 jquery、bootstrap 等。再加上后端语言如 php 或 python 来处理 web 请求和 HTML 模板。

这两种方式你更喜欢哪一种，为什么？

fe-way 看起来很酷，因为你已经将后端逻辑完全分离到一个 REST API 中，现在不管它是 python 还是 php、django 还是 laravel。从某种意义上说，它是经得起未来考验的，不是吗？

然而，我没有看到很多人转向 fe-way，他们仍然在使用 laravel，django，flask 等。那么，是什么在阻碍他们前进呢？这仅仅是对未知的恐惧，还是对用生活方式做事有任何真正的担忧？我能想到的一些基本问题是浏览器不兼容，缺乏一个健壮和稳定的框架(angulars & vues 不断来来去去，或者不断改造自己)，太多包的 npm 问题，等等。

如果这些问题都解决了，我不认为向 fe-way 迈进会有什么大的瓶颈。