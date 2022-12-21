# 使用 Chrome 扩展

> 原文：<https://dev.to/brightdevs/working-with-chrome-extensions-1i1h>

每当你想在 Chrome 浏览器中添加一个脚本时，Chrome 扩展可以让你的生活变得更简单。使用扩展非常容易。这只是一些不错的旧 JavaScript 代码，仅此而已。然而，在使用扩展时，有一些事情值得考虑。

Chrome 扩展文档内容丰富，易于阅读。还有一个[入门](https://developer.chrome.com/extensions/getstarted)教程可用。

## 声明文件

所有项目文件都应该放在一个文件夹中，该文件夹应该以 zip 文件的形式上传到 [Chrome 网络商店](https://chrome.google.com/webstore/)。manifest.json 文件包含所有文件的元数据。这意味着任何文件如果没有在清单中提到，都将被忽略。您最有可能从创建清单文件和声明最重要的字段开始您的项目。如何操作的说明可以在[文档](https://developer.chrome.com/extensions)中找到。大多数文件应该在[“内容脚本”](https://developer.chrome.com/extensions/content_scripts)字段中声明。这些路径是相对于包根目录的。所有的 JavaScript 和 CSS 文件都应该在那里声明。本节中 CSS 文件的出现有点误导，此外，它们也需要在[“web _ accessible _ resources”](https://developer.chrome.com/extensions/manifest/web_accessible_resources)字段中声明。好的一面是，一旦在两个字段中都声明了 CSS 文件，就可以使用了(不需要额外的链接)。

图像应该只在“web_accessible_resources”字段中声明。无论何时你想在你的扩展代码中使用它们，就像这样调用它:

```
var img = chrome.extension.getURL('ImageName'). 
```

文件名需要提供扩展名(例如。svg，。png，。jpg)。

## 激活分机

要激活扩展，您需要在[“content _ scripts”](https://developer.chrome.com/extensions/content_scripts)字段的“matches”部分注册它。您也可以在[【权限】](https://developer.chrome.com/extensions/declare_permissions)字段中设置权限。我使用的是“activeTab”权限，因为它可以防止 Chrome 浏览器显示警告信息，提示应用程序可以访问所有网站上的数据。否则，该消息会在扩展安装过程中弹出。

## 全局变量和常量

可以在. js 文件的全局对象中定义全局变量或常量，如下:

```
const config = {
   host: 'host',
   port: portNumber,
   logVisible: false,
} 
```

要使用它们，只需在脚本文件中键入`config.host`即可。

## 安全

由于安全原因，Chrome Extension 限制您进行某些操作。限制的[列表可以在文档中找到。
但是，你应该记得保护你的脚本免受外部干扰，尤其是跨站脚本攻击。例如，使用`innerHTML`注入内容时要小心。想到用`innerText`代替。不要在 HTTPS 页面上检索 HTTP 内容，因为 HTTP 内容可能已经被恶意网络破坏。如果您尝试这样做，浏览器将显示一条警告消息。](https://developer.chrome.com/apps/contentSecurityPolicy)

## 发布资源

Chrome 扩展可以在 [Chrome 网络商店](https://chrome.google.com/webstore/)中发布，这是非常少数的——你不需要每次有新版本时都把包发给用户。要发布扩展，您需要创建一个开发人员帐户。发布的费用是 5 美元(你支付的是整个服务，而不是一个单一的资源)。小组出版也是可能的，然后每个开发者需要支付 5 美元加入小组。你可以在登录 [Chrome 网上商店](https://chrome.google.com/webstore/)并创建一个开发者账户后购买这项服务。

发布资源最多需要一个小时。每次进行更新时，清单中的[“版本”](https://developer.chrome.com/extensions/manifest/version)字段都需要更改。注意还有一个[“manifest _ version”](https://developer.chrome.com/extensions/manifest/manifest_version)字段，它是清单文件的一个版本。