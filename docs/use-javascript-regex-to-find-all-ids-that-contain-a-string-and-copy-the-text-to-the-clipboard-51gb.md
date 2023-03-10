# 使用 Javascript Regex 查找所有包含字符串的 id，并将文本复制到剪贴板

> 原文：<https://dev.to/jcandan/use-javascript-regex-to-find-all-ids-that-contain-a-string-and-copy-the-text-to-the-clipboard-51gb>

网络抓取是一个强大的工具。我有时发现，为一个简短的任务编写一个成熟的漂亮的 Soup Python 脚本是不必要的。今天我遇到了一个问题，一个网页不允许我选择要复制的表格中的项目，即使可以，我也会在剪贴板中有额外的、不需要的列数据。

## 解决方案:控制台网页抓取

### 我们来分解一下。

首先，我想要的是捕捉每个元素的方法。我希望从表中得到的文本被包装在一个`<div id="edit-tid-24-view"></div>`标签中。我尝试通过“开始于”过滤器首先锁定它们:

```
document.querySelectorAll('[id^="edit-tid"]'); 
```

这使我走了一段路，但是我需要目标 ID 属性值不仅以此开始，而且以`-view`结束。在典型的正则表达式中，您可能会做类似于`/edit-tid.*-view/`的事情。有点贪心，但对我来说已经够了。然而，我们在`querySelectors`并没有真正使用正则表达式。因此，我组合了两个过滤器:一个用于开始部分，另一个用于结束部分。

```
document.querySelectorAll('[id^="edit-tid"][id$="-view"]'); 
```

之后就相当简单了。我想遍历返回的`NodeList`对象，所以我必须先把它转换成一个`Array`。

```
Array.from(someObject); 
```

在那里，我可以将 DOM 中每个`Node`的`innerText`映射到所需字符串的数组。

```
Array.from(someObject).map(function(item) { return item.text; }); 
```

然而，我对此并不满意。

我希望我的列表干净地输出，并直接传输到我的剪贴板。Javascript 允许用户在`document`对象上选择并执行复制命令。然而，我在控制台中工作时，发现了一些更简单的东西:`copy`函数在控制台中工作。

我只是用回车将字符串连接在一起，并将结果复制到我的剪贴板上。

## 结论

这是我的开发者工具控制台 web scraper 的全部荣耀。

```
copyText = ''; 
Array.from(
    document.querySelectorAll('[id^="edit-tid"][id$="-view"]'))
    .forEach(function (x) { 
        copyText += x.text + '\n' 
    }
); 
copy(copyText); 
```