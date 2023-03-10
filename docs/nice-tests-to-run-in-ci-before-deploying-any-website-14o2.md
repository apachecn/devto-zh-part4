# 在部署任何网站之前，在 CI 中运行良好的测试

> 原文：<https://dev.to/andyli/nice-tests-to-run-in-ci-before-deploying-any-website-14o2>

因为我是我的创业背后唯一的开发者🎁，我必须负责从开发到部署的所有技术事务。尽管我一直遵循最佳实践，并对边缘情况保持谨慎，但我仍然不太相信自己编写的代码。

在部署之前，我在 CI 中添加了一些测试来检查我的网站，他们已经多次发现了我的错误。最酷的是，这些测试中的许多足够通用，适用于任何网站，所以你也应该考虑使用它们！

## 1。标记验证

第一个容易实现的结果是 HTML(和 CSS/SVG)验证。大多数 HTML 模板并不强制使用有效的 HTML，所以输出带有不匹配标签的文档是很容易的，这是很痛苦的。Giffon 使用了 [ReactDOMServer](https://reactjs.org/docs/react-dom-server.html) ，这可以防止标签不匹配，但验证仍然让我多次注意到将块级元素(例如`<ul>`)放在`<p>`中，并且在`<img>`中还缺少`alt`属性。

Giffon 的 CI 针对开发服务器使用了 [html5validator](https://github.com/svenkreiss/html5validator) 命令行工具。它基本上调用如下命令:

```
html5validator --html http://localhost:3000/every/page
html5validator --css www/css/*.css
html5validator --svg --errors-only wimg/*.svg 
```

## 2。检测浏览器控制台错误消息

第二个一般性错误检测是检查浏览器控制台。它还报告 JS 运行时错误以及损坏的图像引用和一些无效的 HTTP 响应头。

Giffon 的做法是使用 [Selenium web 驱动](https://www.seleniumhq.org/projects/webdriver/)，导航到每个页面并调用`driver.get_log("browser")`，断言没有日志。

## 3。检测水平滚动条

大多数网站不使用水平滚动条。当一个由于布局错误而出现时，这很烦人(尤其是在移动设备上)。

吉丰的 CI 再次利用了硒。CI 导航到每个页面，并通过断言以下代码返回零来检测水平滚动条的存在:

```
driver.execute_script(
    "document.scrollingElement.scrollLeft = 1;" +
    "return document.scrollingElement.scrollLeft;"
) 
```

## 4。拼写检查

谁不打错别字？这可能并不重要，但是你的网站中有错别字确实会给访问者留下不好的印象。

Giffon 的 CI 使用[拼写检查 npm 库](https://www.npmjs.com/package/spellchecker)。当我实现多语言支持时，Giffon 将其 UI 文本字符串隔离，因此我可以轻松地迭代所有字符串并调用`SpellChecker.checkSpelling(str)`。

如果您没有隔离字符串，您可以将 html 输出保存为一个文件，并使用传统的 [aspell](http://aspell.net/) cli 列出所有拼写错误的单词，如下所示:

```
cat path/to/my.html | aspell --mode=html list 
```

# 还有别的吗？

您是否在 CI 管道中运行类似的测试？有什么好的分享给我吧！