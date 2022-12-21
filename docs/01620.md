# HTML 解析器——一次设计，随处使用

> 原文：<https://dev.to/sm0ke/html-parser-design-once-use-anywhere-1jnl>

各位编码员好，

这篇文章是关于一个(WIP) [HTML 解析器](https://appseed.us/developer-tools/html-parser)工具的，它的目标是自动化 web 开发过程中的一组常见任务。

* * *

> 想进一步了解这个话题？访问 AppSeed 平台，获取更多与 [HTML 解析](https://blog.appseed.us/tag/html-parser/)和[开发者工具](https://appseed.us/developer-tools)相关的文章。**谢谢你！**

* * *

## 2019 年  [HTML 解析器](https://appseed.us/developer-tools/html-parser)？

是的，在你的日常工作流程中编写和使用[开发者工具](https://appseed.us/developer-tools/)，有时(并不总是)可能会帮助你跳过日常工作中的手工或重复工作。

对于新手来说，**解析**是根据正式语法的规则(维基百科的定义)分析一串符号的过程，无论是自然语言还是计算机语言。这是极客对解析的定义。

对于人类来说， [HTML 解析](https://appseed.us/developer-tools/html-parser)意味着使用工具和算法理解和操纵文本、文档或 HTML 内容。

* * *

## 为什么要写这样一个工具

最初，我决定编写一个简单的 [HTML 解析器](https://appseed.us/developer-tools/html-parser)来自动将 UI 集成到新项目中。我用以前的[文章](https://dev.to/sm0ke/html-parser-extact-html-information-with-ease-308m)中的一些代码片段几乎完全取代了手工工作:

*   加载 HTML 文件并改变 DOM
*   管理页面资产(Javascript、CSS)以更新用 Python、JAMstack、Php 编码的各种样板文件的路径。
*   编辑 DOM 元素(锚、div、图像)并改变属性(图像 src、锚 HREF 属性等)
*   为各种模板引擎提取组件:Jinja、Blade
*   用生产就绪变量替换硬编码文本

* * *

> 使用当前版本的 **[HTML 解析器](https://appseed.us/developer-tools/html-parser)** [我已经构建了 100 多个开源应用](https://dev.to/sm0ke/100-open-source-apps-built-with-automation-tools-3jmj)，并在 Github 上发布了所有源代码。

* * *

为什么不在这里停下来呢？很快我意识到我可以扩展解析器来减少我和其他人做的手工工作。关于 2019 年网页设计市场的一些事实(至少在我的脑海中):

*   Bootstrap / jQuery 主题仍然主导着市场
*   Bootstrap 主题市场拥挤且过度饱和。
*   它们不是现代科技的主题:Vue，Angular React
*   新的 CSS 框架(布尔玛，顺风)在 Bootstrap 前面获得牵引力，但是..没有那么多的主题和 UI 工具包来快速启动一个新项目。

考虑到这一点，我决定再添加一些功能，以便减少更多由我或其他人完成的手工工作:

* * *

## 将自举主题转换为 Vue，React

对于相同的产品，Vue 和 React UI 套件比 Bootstrap 版本更贵，主要基于流程中涉及的手工工作。

> 为什么不把 Bootstrap/jQuery 主题自动转换成其他相关技术，比如 React、Vue、Angular

* * *

## 将[自举](https://getbootstrap.com)主题迁移到[布尔玛](https://bulma.io/)和[顺风](https://tailwindcss.com/)

可能听起来有点未来派，但是看看这个工具:[tail windo](https://github.com/awssat/tailwindo)--“将引导 CSS 代码转换为 Tailwind CSS 代码”。在我看来，这是一个绝妙的主意。

这个过程非常简单:Tailwindo 扫描 PHP 文件，并使用对应映射将引导类替换为 Tailwind 类。

使用相同的方法，我将在解析器中添加引导转换特性，将引导主题转换为布尔玛和顺风

* * *

### [自举到布尔玛 CSS](https://appseed.us/developer-tools/bootstrap-to-bulma-css)

完整的映射列表可以在[这里](https://bulma.io/alternative-to-bootstrap/)找到。我将只列出几个映射(引导左侧):

*   行->列
*   列->栏
*   label -> label
*   表单控件->文本区
*   关闭->删除

* * *

### [自举顺风](https://appseed.us/developer-tools/bootstrap-to-tailwind-css)

完整的映射列表可以在 [Tailwindo 存储库](https://github.com/awssat/tailwindo/blob/master/src/Converter.php)上找到。请在下面找到一些样品(自举左侧):

*   xs -> sm
*   原色->蓝色
*   成功->绿色

* * *

## 结尾一注

新特性是一个 WIP，我希望在今年年底前可以投入生产，源代码提交到 [HTML 解析器](https://github.com/app-generator/html-parser)库中。

* * *

## 资源

*   [HTML 解析器](https://appseed.us/developer-tools/html-parser) -由 AppSeed 支持
*   [HTML 解析器——如何用 Python BS4 少干活](https://blog.appseed.us/html-parser-python-beautifulsoup-use-cases/)
*   [开发者工具-开源 HTML 解析器](https://blog.appseed.us/developer-tools-html-parser/) -相关文章
*   [BeautifulSoup Html 解析器](https://www.crummy.com/software/BeautifulSoup)文档
*   HTML 解析器-将 HTML 转换成 Jinja2 和 Php 组件 -相关博客文章