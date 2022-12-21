# Kimai2: PDF 导出时的日语乱码

> 原文：<https://dev.to/nabbisen/kimai2-pdf-efk>

[Kimai 2](https://www.kimai.org/) 是用于时间跟踪的 web APP。
在 MIT 许可证下，公开为开源。

在 v1.0 中进行 PDF 导出时，日语中会出现乱码。
(对于 Excel 和 CSV 导出，用日语也可以正常输出。 ）

PDF 导出中使用了[PHP](https://php.net) 的 PDF 生成库[mpdf](https://github.com/mpdf/mpdf) 。
原因是这边的字体设定不足。

作为应对，通过如下所示变更 src 的代码，即使是日语也能不乱码地输出。

`kimai2/src/Utils/MPdfConverter.php` :

```
- $mpdf = new Mpdf(['tempDir' => $this->cacheDirectory]); + $mpdf = new Mpdf(['tempDir' => $this->cacheDirectory,
+     'fontdata' => [
+         'sun-exta' => [
+             'R' => 'Sun-ExtA.ttf'
+         ]
+     ]
+ ]); 
```

Enter fullscreen mode Exit fullscreen mode