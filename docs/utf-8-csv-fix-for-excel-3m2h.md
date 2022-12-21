# Excel 的 UTF-8 csv 修复程序

> 原文：<https://dev.to/camillehdl/utf-8-csv-fix-for-excel-3m2h>

对 CSV 文件使用 utf-8 听起来很明显，并且在大多数软件处理它们时效果很好。

大多数软件除了微软的 Excel，考虑到它的用户数量，会让你一天都难过。

Excel 似乎假定*windows-1252*T2，除非提供了[字节顺序标记](https://en.wikipedia.org/wiki/Byte_order_mark)。

要解决这个问题而不要求用户浏览隐藏菜单的迷宫，您可以在将字符串保存到文件或触发下载之前将 BOM 添加到字符串中。

在 JavaScript 中:

```
let csvString = ["a,b,c", "1,2,3"].join("\n");
csvString = "\ufeff" + csvString; 
```

Enter fullscreen mode Exit fullscreen mode

或者用 PHP:

```
$csvString = implode("\n", ["a,b,c", "1,2,3"]);
$csvString = chr(0xEF) . chr(0xBB) . chr(0xBF) . $csvString; 
```

Enter fullscreen mode Exit fullscreen mode

如果您查看十六进制转储，您可以在最开始检查这个字节序列的存在:`EF BB BF`。