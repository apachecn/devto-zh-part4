# 基于工作表的 CASD #2

> 原文：<https://dev.to/bugmagnet/sheets-based-casd-2-ef0>

你可能还记得第一篇文章，这都是在 Google Sheets 和 Google Apps 脚本的环境下完成的。

在之前的文章中，我们创建了一个电子表格，用名称和类型描述了 JSON 对象的结构。然后，我们为该结构的每个实例制作了列。我们使用一个用户定义的函数生成 JSON，并将其显示在一个单元格中，从这里可以复制它。

我厌倦了最后一步，因为它也是用双引号括起来的，而且每个双引号都被加了一倍。因此，从电子表格到文本编辑器再到目的地。

那么，如果一个人可以将结果传输到某个地方，从而避免这场引用的噩梦，又会怎样呢？如果能够打开一个文件并复制文本不是更好吗？(嗯....一个按钮，可以复制到剪贴板会很酷。也许下次吧。)

### 服务器端

我们有几个 Azure 服务器。在其中一个上，在我们保存大多数`.ashx`处理程序的地方，我放了下面的

```
<%@ WebHandler Language="C#" Class="SettingsMaker.Handler" %>
using System;
using System.Web;
using System.Collections.Generic;
using System.Collections.Specialized;
using System.Net;
using System.Diagnostics;
using System.IO;

namespace SettingsMaker
{
    public class Handler : IHttpHandler
    {
        private readonly System.Object threadLock = new System.Object();

        public void ProcessRequest(HttpContext context)
        {
            string fn = context.Request.QueryString["fn"];
            string postBody = string.Empty;
            using (var reader = new System.IO.StreamReader(context.Request.InputStream, System.Text.Encoding.UTF8))
            {
                postBody = reader.ReadToEnd();
            }

            lock (threadLock)
            {
                var target = @"C:\temp\Settings";
                System.IO.Directory.CreateDirectory(target);
                File.WriteAllText(Path.Combine(target, fn + ".json"), postBody);
            }

            context.Response.ContentType = "text/json";
            context.Response.Write("{}");
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

可能有些陈述是多余的。无论如何，你明白了:有一个 ASP 处理程序在等我发送代码，它会把所有东西保存在`c:\temp\Settings`中。文件名将放在`fn`变量中，文本将放在 POST 主体中。每次运行时，它都会覆盖现有的内容，而`lock`是为了防止它在另一个实例写入同一文件时尝试写入。

### 客户端

好了，现在谷歌床单结束了。我张贴的电子表格图像基本上是相同的，除了我们现在有一行用于从列的顶行获取的`functionName`。它成为服务器上文件的名称。

源在 TypeScript 中。

```
function GenerateSettings(valueRange: any[], settingsRange: any[]) {
  const settings: any = {};
  const settingsValues = settingsRange; //.getValues();
  let offset = 0;
  let sj;
  for (const cell of valueRange) {
    const nameType = settingsValues[offset];
    let name = nameType[0];
    const type = nameType[1];
    const cellValue = cell[0];
    offset++;
    let formattedCellValue: any;
    switch (type) {
      case "object":
        formattedCellValue = cellValue === "" ? '{}' : cellValue;
        break;
      case "object[]":
      case "number[]":
        formattedCellValue = cellValue === "" ? '[]' : '[' + cellValue.split(/,\s*/g).join(",") + ']';
        break;
      case "string[]":
        formattedCellValue = cellValue === "" ? '[]' : '[' + cellValue.split(/,\s*/g).map((elt: string) => "'" + elt + "'").join(",") + ']';
        break;
      case "number":
        formattedCellValue = cellValue === "" ? -1 : cellValue;
        break;
      case "boolean":
        formattedCellValue = cellValue === "" ? 'false' : (cellValue ? 'true' : 'false');
        break;
      case "literal":
        formattedCellValue = cellValue === "" ? '""' : cellValue; // was eval
        break;
      case "string":
        formattedCellValue = cellValue === "" ? '""' : '"' + cellValue + '"';
        break;
    }
    name = "['" + name.split(/\./g).join("']['") + "']";
    let js = "if (!settings) settings = {}; settings" + name + "=" + formattedCellValue + ";";
    eval(js);
  }
  const result = "var settings = " + JSON.stringify(settings, null, '  ');
  SettingsMaker(settings.functionName, result);
  return result;
}

function SettingsMaker(name: string, body: string): void {
  const f = UrlFetchApp.fetch("http://some.server.somewhere/SettingsMaker.ashx?fn=" + name, {
    'method': 'post',
    'muteHttpExceptions': true,
    'payload': body
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，传输生成代码的是对 SettingsMaker()的调用，`UrlFetchApp.fetch`调用的第二个参数定义了方法和有效负载。

现在，因为我在服务器上打开了一个 RDP 会话，所以我可以导航到`c:\temp\Settings`，打开相关的`.json`文件，直接复制到我的项目中，而不必处理双重双引号之类的东西。

可能有其他方法可以做到这一点，但这是目前工作还好。