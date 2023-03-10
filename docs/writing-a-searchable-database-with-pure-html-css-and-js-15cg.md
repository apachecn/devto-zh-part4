# 用纯 HTML、CSS 和 JS 编写一个可搜索的“数据库”

> 原文：<https://dev.to/micahlt/writing-a-searchable-database-with-pure-html-css-and-js-15cg>

> 这就是我如何创造了 https://micahlt.github.io/renart/。

大约一周前，我发现自己在学校遇到了困难。我有一个 AP 欧洲历史作业，要求我找到很多关于文艺复兴时期艺术品的信息。我心想，“应该有一个包含这些信息的基本数据库。”

在中午 12 点完成作业后，我决心为可能遇到同样问题的后代找到一个解决方案。我喜欢 HTML 和 CSS，但不了解 SQL(或其任何衍生物)，所以我需要用纯 HTML 和 CSS(可能还有一点 JavaScript)建立一个“数据库”。以下是我根据自己的知识和从 Stack Overflow 中搜集的一些资料得出的结论。

一开始，我创建了一个新的 GitHub 库。我创建了一个`index.html`文件，从基本语法开始:

`<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
My Database
<style>
</style>
</head>
<body>
</body>
</html>`

接下来，我做了一个简单的输入框。

`<input type="text" onkeyup="searchFunction()" placeholder="search here" title="search">`

如果你不熟悉这里发生的事情，我们正在创建一个默认的`input`文本框，并添加占位符文本“搜索这里”。`onkeyup`指定当 enter 键被按下并且`input`有一个值时，我们将调用一个 JavaScript 函数。

在这之后，我创建了一个表:

`<table align = "center">
<tr class = "header">
<th style="width:25%;">Title</th>
<th style="width:25%;">Artist</th>
<th style="width:25%;">Commisioner</th>
<th style="width:25%;">Completed</th>
</tr>
</table>`

基本上，我在这里做的是为表格构建标题。由于 JavaScript 将如何编写，这将是不可搜索的。我也在桌子中央。在这之后，我只是在标题后面使用另一个`<tr>`元素添加了一些行。

`<tr>
<td>The Annunciation</td>
<td>Fra Angelico</td>
<td>Cosimo de' Medici</td>
<td>1446</td>
</tr>
</table>`

您可以在每一列中添加自己的数据(`<td>`)。接下来的部分对我来说很难。我喜欢关注 HTML 和 CSS，但我喜欢假装 JavaScript 不存在。与 Python 和 Ruby 相比，它似乎有点复杂。我滚动堆栈溢出一段时间，直到我找到了完美的脚本:

`<script>function artFunction() {
// Declare starting variables
var input = document.getElementById("artInput");
var filter = input.value.toUpperCase();
var table = document.getElementById("artTable");
var trs = table.tBodies[0].getElementsByTagName("tr");
// Loop through rows
for (var i = 0; i < trs.length; i++) {
// Define the cells
var tds = trs[i].getElementsByTagName("td");
// hide the row
trs[i].style.display = "none";
// loop through row cells
for (var i2 = 0; i2 < tds.length; i2++) {
// if there's a match
if (tds[i2].innerHTML.toUpperCase().indexOf(filter) > -1) {
// show the row
trs[i].style.display = "";
// skip to the next row
continue;
}
}
}
}
</script>`

把这个加在你`<body>`的底部就行了。我甚至无法解释这是怎么回事。现在你完成了！老实说，这不是保存数据库的最佳方式，因为你的 HTML 页面会随着你添加的每一项而增长。不过，如果你不想学 SQL 的话还是可以的。

**TL；DR-如果你想建立一个没有 SQL 的“数据库”，访问 https://github.com/micahlt/renart 的[，查看`art.html`文件。](https://github.com/micahlt/renart)**