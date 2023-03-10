# 最奇怪的 JavaScript 行为是什么？

> 原文：<https://dev.to/ben/what-is-the-oddest-javascript-behavior-4dh2>

JavaScript 因其在许多领域的不一致性而臭名昭著。它也很强大，很受欢迎，有很多优势。

但是我们可以拿奇怪的事情开玩笑吗？(无论该行为总体上是否是一件好事)

让我们从一个例子开始:

<header>![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [answer re: Why is the result of ('b'+'a'+ + 'a' + 'a').toLowerCase() 'banana'?](https://stackoverflow.com/questions/57456188/why-is-the-result-of-ba-a-a-tolowercase-banana/57456236#57456236) Aug 12 '19[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)618![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/57456188/why-is-the-result-of-ba-a-a-tolowercase-banana/57456236#57456236) </header>

`+'a'`解析为`NaN`(“不是数字”)，因为它将字符串强制转换为数字，而字符`a`不能解析为数字

```
document.write(+'a');
```

To lowercase it becomes `banana`.

将`NaN`加到`"ba"`会因类型将`NaN`变成字符串`"NaN"`

[Open Full Answer](https://stackoverflow.com/questions/57456188/why-is-the-result-of-ba-a-a-tolowercase-banana/57456236#57456236)