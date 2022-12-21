# 辅助功能提示:设置语言

> 原文：<https://dev.to/chrishall78/accessibility-tips-setting-language-13f9>

当您正在开发以满足 WCAG 2.0 标准时，它有助于设置页面的语言。具体来说， [3.1.1 -页面语言](https://www.w3.org/WAI/WCAG21/quickref/#language-of-page)规则。

你可以用 **html** 标签上的 **lang** 属性来完成，比如:

```
<html lang="en"></html> 
```

除了[语言代码](https://www.w3schools.com/tags/ref_language_codes.asp)之外，您还可以添加一个[国家代码](https://www.w3schools.com/tags/ref_country_codes.asp)，使语言更加具体。例如，这将表示英国英语:

```
<html lang="en-GB"></html> 
```

如果您想将一段特定的内容设置为不同的语言，也可以在那里使用 **lang** 属性。这将有助于屏幕阅读器正确读出内容。

例如，一个链接到一个网站的日语版本将被读作“Nihongo”，而不是“表意字符表意字符表意字符”。

```
<a href="#" lang="ja">日本語</a> 
```