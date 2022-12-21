# CFScript 陷阱和语法指南

> 原文：<https://dev.to/mikeborn/cfscript-gotchas-and-syntax-guides-4j0o>

现在，脚本语法通常是简单明了的，但是和任何语言一样，有时很容易混淆脚本语法中的某些东西。在这篇文章中，我将介绍 CFScript 中常见“陷阱”的快速列表，记录每个陷阱的正确语法，最后链接到一个 CFScript 指南列表，它可能会帮助您立即开始编写 CFScript。

## CFScript 中常见的“陷阱”

*   太多英镑符号
*   缺少分号
*   新运算符
*   赋值而不是相等运算符
*   严格相等运算符

### 太多英镑符号

不要这样:

```
if ( #isBroken# ) {
    writeOutput( "Hello #name#!" );
    writeOutput( #age# );
} 
```

为什么不呢？因为英镑符号只在字符串中需要。我认为正确的术语是“字符串求值”，因为你的变量需要被求值，并且内容被放在字符串中。

上面这个例子的正确语法是:

```
if ( isBroken ) {
    writeOutput( "Hello #name#!" );
    writeOutput( age );
} 
```

更详细的文章，请查看马克·克鲁格的[“被英镑符号决策迷惑”](https://www.coldfusionmuse.com/index.cfm/2011/2/10/when.do.i.use.pound.signs)博客文章。

### 缺少分号

这是一个相当简单的问题。分号用于终止 CFScript 语句，在 CF2016 之前的 ACF 中是**必需的**。分号现在在 CF2018 中是可选的，Lucee 也是。所以如果你得到一个神秘的*“无效结构。在第 x 行"*出现错误时，您可能要检查您的分号！

### 新增操作员

如果你使用 CFML 的类似 JS 的语法，你也应该使用新的类似 JS 的操作符，不是吗？这是个人喜好的问题，但是我觉得这些操作符在所有语言中都更“标准”,并且对 CF 开发者来说是一致的、改进的体验。

*   用`!`代替`NOT`
*   用`==`代替`EQ`
*   用`!=`代替`NEQ`
*   用`>`代替`GT`
*   用`<`代替`LT`
*   用`>=`代替`GTE`
*   用`<=`代替`LTE`
*   用`||`代替`OR`
*   用`&&`代替`AND`

旧的基于标记的操作符:

```
if ( NOT json.success OR arrayLen(validation.errors) GT 0 ) {
    // do something
} 
```

更新的类似 JS 的操作符:

```
if ( !json.success || arrayLen(validation.errors) > 0 ) {
    // do something
} 
```

PS。`ArrayLen()`总是返回一个非负整数。确实没有必要检查它是否大于零，因为[在 CFML 中任何非零整数的值都将为真。而零总是评估为假](https://www.coldfusionmuse.com/index.cfm/2010/2/5/Booleans.and.Coldfusion)。因此，您可以像这样进一步缩小条件:

```
if ( !json.success || arrayLen(validation.errors) ) {
    // do something
} 
```

## 使用赋值运算符而不是相等运算符

我觉得这是所有开发者的必经之路。如果您不小心在一个条件中使用了赋值运算符(`=`)而不是等式运算符(`==`)，您会得到一些非常有趣的结果。

例如，这段代码将总是在 Lucee 中打印`True!`。

```
if ( a = 1 ) {
  writeOutput("True!");
} else {
  writeOutput("False!");
} 
```

这是一个很难找到的 bug，因为它在第一次扫描时看起来很像。所以，要有意识地去写你的条件句！

## 严格相等运算符

这几乎是个恶作剧的问题——在 CFML 没有“严格平等”的运算符！

所以不要这样做，因为这样做是行不通的:

```
if ( myVar === "theValue" ) {
    // do something
} 
```

如果你必须强制一个类型，使用`isValid()`。

## CFScript 语法指南

我想我已经整理了一个快速(非详尽)的 CFScript 语法指南列表，可以帮助人们踏上现代 CF 之旅。您可能希望将其中的一些列为书签，如 Tony Junkes 的标记和脚本语法比较，以及 Ortus 的“100 分钟”完整 CFScript 指南，最后是 Pete Freitag 的备忘单。如您所见，有大量的 CFScript 文档。

*   托尼·容克将 CFML 标签转换成剧本
*   [CFScript 备忘单](https://www.petefreitag.com/cheatsheets/coldfusion/cfscript/)作者[皮特·弗莱塔格](https://www.petefreitag.com)
*   [100 分钟了解现代 CFML](https://modern-cfml.ortusbooks.com/)由[奥特斯解决方案](https://cfdocs.org)
*   [CFScript 语法指南](https://cfdocs.org/script)CFDocs.org[上](https://cfdocs.org)
*   Adam Cameron 撰写的 CFScript 指南
*   Adobe 的 CFScript 用法
*   [使用 CFScript 语句](https://helpx.adobe.com/coldfusion/developing-applications/the-cfml-programming-language/extending-coldfusion-pages-with-cfml-scripting/using-cfscript-statements.html)，也是由 Adobe
*   [CFScript 语言](https://helpx.adobe.com/coldfusion/developing-applications/the-cfml-programming-language/extending-coldfusion-pages-with-cfml-scripting/the-cfscript-language.html)，然而*另一个* Adobe CFScript 指南

希望这有所帮助-感谢您的阅读！