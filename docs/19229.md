# 为什么我想在 LibreOffice Calc 中使用 JSON。

> 原文：<https://dev.to/arachan/why-i-want-to-use-json-in-libreoffice-calc-3lkb>

最近 Web Service 导出 JSON。不是 SOAP，不是 XML。
Web 数据是 JSON。

*   从邮政编码搜索地址
*   天气

....等等

如果 Calc 可以使用 JSON 和 WebAPI，
Calc 借用 Web 的函数。

用 Python，很简单。是否需要由 calc 使用？

号
上班族不用 Python。
他们说。
VLOOKUP 太棒了！VBA 太棒了！
巨蟒？什么？
我不是程序员。

我们需要在 Calc 中处理 JSON 和 WebAPI。

Excel 可以用 JSON。
[连接一个 json 文件](https://support.office.com/en-us/article/connect-to-a-json-file-f65207ab-d957-4bf0-bec3-a08bb53cd4c0?ui=en-US&rs=en-US&ad=US)
LibreOffice 没有这个功能。

你做这个函数。

所以，我开发了下划线计算器扩展。

*   [GetRest 函数](https://github.com/arachan/getrest)
*   [ParseJSON 函数](https://github.com/arachan/parsejson)

可能还不够。
ParseJSON 不工作:-(

最后，我想让 Calc Standerd 函数。