# 空白是邪恶的..你的弦的角度是多少？？角度处理 i18n 的指南和最佳方法

> 原文：<https://dev.to/sanketmaru/whitespaces-are-evil-how-angular-translates-your-string-guidelines-and-best-approaches-for-handling-i18n-in-angular-10l2>

**简要概述**

开发人员会遇到这样的场景

*   空格被添加到你的字符串中，以及这些字符串是如何被翻译的。
*   数字、日期、百分比是如何翻译的？
*   我们怎么能给巴顿发 i18n 条短信。让翻译文件没有重复条目的最佳方法是什么？

Angular 非常好地支持国际化，官方文档中有设置和应该处理 i18n 的不同情况。

这篇文章涵盖了开发人员在提交新代码时需要关注 i18n 的场景。

这篇文章是写给开发人员和那些如果没有满足下面的建议就审查拉请求的人的

**建议**

*   HTML 代码中每个可见的英文字符串都应该用 i18n 标记

```
<h2 i18n="Page Heading"> Examples of Internationalization(i18n) </h2> 
```

大多数情况下，所有这些可见行都应该放在 HTML 中，而不是 TS 文件中。

*   除了 i18n 标签之外，建议为字符串添加含义和描述，这有助于翻译团队以更好的方式进行翻译

```
<ul>
  <li i18n="First Name|First Name of candidate">First Name</li>
  <li i18n="Middle Name|Middle Name of candidate">Second Name</li>
  <li i18n="Last Name|Last Name of candidate">Last Name</li>
</ul> 
```

我们将考虑这样一种情况，其中只给出了 i18n 标签，并且对于相同类型的 html 代码也给出了含义和描述。

```
<ul>
  <li i18n>First Name</li>
  <li i18n>Middle Name</li>
  <li i18n>Last Name</li>
</ul> 
```

```
<ul>
  <li i18n="First Name|First Name of candidate">First Name</li>
  <li i18n="Middle Name|Middle Name of candidate">Second Name</li>
  <li i18n="Last Name|Last Name of candidate">Last Name</li>
</ul> 
```

当为它们中的每一个生成消息文件时，条目被添加。上面的代码片段也是一样的，但是它在消息文件中添加了不必要的条目。这对开发人员来说是第一个危险信号，可以检查代码库中的相似代码，并为它们创建一个单独的条目。

*   当您有嵌套标签时，在父标签上应用 i18n 标签

```
<div i18n="Signup success|Success message for signup ">Your signup is successfull.
<span><strong>Please verify your email address</strong> to continue.</span>
</div> 
```

包含在父标签中确保文本被正确翻译，因为单个句子与分解成多个句子相比会有不同的含义。

*   在字符串中找到动态参数时，Angular 负责插值

```
<div i18n="License status|Different statuses of licenses">License {{ licenseStatus }}</div> 
```

*   建议您不要在字符串中使用冒号字符(:)。有些语言不转换它。

*   如果您有一个带`{{ firstName }} {{ lastName }}`的字符串，它需要使用 i18n 标记进行国际化，因为一些地区有不同的名称格式规则。

*   需要使用角线来格式化数字、日期和百分比。

*   空白是邪恶的。确保不要在字符串中添加空格，因为“演示文本”和“演示文本”会导致两种翻译

*   按钮
    在整个应用程序中，大多数按钮文本都是相似的。所以按钮应该用一个静态 id 来标记，这样就不会有重复的翻译，并且在整个代码库中使用相似的 id。

下面是一些常用的按钮和一个使用静态 id 的例子。

```
<button (click)="cancel()" i18n="Cancel forgot password|secondary button@@btnCancel">Cancel</button> 
```

| 纽扣 | 身份 |
| --- | --- |
| 取消 | btnCancel |
| 关闭 | btnClose |
| 好 | 好吧 |
| 救援 | btnSave |
| 是 | 本特斯 |

希望这篇文章有助于包含 i18n 的 UI 指南中的要点。