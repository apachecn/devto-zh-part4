# 问题:使用 Javascript 代理对象时调用目标父对象的静态方法

> 原文：<https://dev.to/ionellupu/problem-calling-static-methods-of-target-s-parent-when-using-a-javascript-proxy-object-2a55>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [代理:使用代理对象时调用目标父对象的静态方法](https://stackoverflow.com/questions/57831932/proxies-calling-static-methods-of-targets-parent-when-using-a-proxy-object)

Sep 7 '19 Comments: 1 Answers: 1[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)0![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/57831932/proxies-calling-static-methods-of-targets-parent-when-using-a-proxy-object) </header>

对于 Javascripters 开发者来说，这是一个非常有趣的问题

在 **Javascript** 中，使用 **[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)** 获取属性时可能会被拦截。

此外，通过如下所示的小技巧，可以在获取类的静态属性时进行拦截:

```
class Handler{
    constructor(object){
        this.object = object;
    }

    get(target, property){
        if
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57831932/proxies-calling-static-methods-of-targets-parent-when-using-a-proxy-object)</button>