# 从“原始”规范创建 RIS 映射

> 原文：<https://dev.to/citation-js/creating-a-ris-mapping-from-the-original-specification-1kfo>

所以不久前，我又在四处寻找 RIS 规范。我之前没有找到它，只有 Zotero 的一个参考实现，维基百科上一个惊人完整的标签和类型列表，以及一些从各种网站和程序导出 RIS 文件的例子。然而，他们似乎并不一致。这里和那里的标签有一些细微的差异，维基百科列出的一堆有用的标签在 Zotero 代码库中被标记为“退化”,并且只用于导入——这意味着某种问题。

会发生什么事？嗯，我再次查看了维基百科页面上的参考资料，看看是否真的没有官方规范或其他更可靠的信息来源。突然，这次*变成了*真正的来源。我不知道我之前是如何错过的，但是有一个页面([存档](https://web.archive.org/web/20120526103719/http://refman.com/support/risformat_intro.asp))链接到一个 zip 文件，其中包含一个 PDF 文件和一个 Excel 文件，其中包含所有不同类型的属性列表。

这听起来很有用，所以我花了 *waaayy* 很多时间自动化[一个脚本](https://github.com/citation-js/ris-mappings)来将这些表单——以及一堆用户输入——转换成 Citation.js 的可用映射。我今天刚刚完成，除了一些……有问题的映射，但我想至少用一个例子来测试最终的脚本。至于结果，嗯，自己看吧。[例子](https://en.wikipedia.org/wiki/RIS_(file_format)#Example_multi-record_format)，来自维基百科页面(CC-BY-SA 3.0 未发布)

```
TY - JOUR
T1 - On computable numbers, with an application to the Entscheidungsproblem
A1 - Turing, Alan Mathison
JO - Proc. of London Mathematical Society
VL - 47
IS - 1
SP - 230
EP - 265
Y1 - 1937
ER - 
```

我的结果是

```
{  "issue":  1,  "page":  230,  "type":  "article-journal",  "volume":  47  } 
```

这看起来真的很奇怪和令人失望。再说一次，这里可能会发生什么？嗯，维基百科上的例子是使用`T1`、`A1`、`JO`和`Y1`，而说明书上说在这里使用`TI`、`AU`、`T2`和`PY`。这些差异是从哪里来的？

在维基百科上做了一些挖掘后，我发现[有一条评论](https://en.wikipedia.org/wiki/Talk:RIS_(file_format)#Versions_of_the_specification)说事实上有规范:一个来自 2011 年，一个来自之前。我查阅的存档规范是 2012 年的(维基百科上有链接！)，而他们使用的是 2011 年以前的版本；幸运的是[仍然可用](https://web.archive.org/web/20110930172154/http://www.refman.com/support/risformat_intro.asp)。未完待续。