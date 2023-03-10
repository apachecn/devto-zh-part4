# 最佳面向开发者的 Craft CMS 插件

> 原文：<https://dev.to/piotrpog/best-developer-oriented-craft-cms-plugins-323h>

这篇文章最初发表在[craftsnippets.com](http://craftsnippets.com?s=dt)上。去那里寻找更多与工艺相关的文章，并准备好使用模板组件。

* * *

这些插件不会给网站增加任何新功能。他们的重点是让开发人员的生活更轻松，让开发过程更有效率。在网站投入生产后，我通常只是禁用或卸载它们，以减少需要定期更新的插件数量。

**[CP 实地考察](https://plugins.craftcms.com/cp-field-inspect)**
我个人的最爱。CP 字段检查条目、类别、全局中的链接字段实例到它们的设置页面。这个插件可能已经帮我节省了几个小时点击控制面板的时间。

**[模板注释](https://plugins.craftcms.com/templatecomments)**
这个插件在渲染的 HTML 中使用`{% block %}`和`{% include %}`标签的地方添加 HTML 注释。这些注释使呈现的 HTML 源代码更具可读性。

**[Twig profiler](https://plugins.craftcms.com/twig-profiler)**
Twig profiler 允许您检查模板的特定片段执行和渲染需要多长时间。在用一个惟一的关键字将模板的一部分包含在`profile`标签中之后，您将在 Yii profiler 图形中找到它们。

**[命令面板](https://plugins.craftcms.com/command-palette)**
这个插件让你只需几个按键就可以在控制面板中导航。它引入了 command palette，这是一个基于 ide 和代码编辑器中使用的类似功能的工具。

**[库存](https://plugins.craftcms.com/inventory)**
库存为控制面板设置部分的标准字段列表提供了更好的替代选择。这个插件显示了**在哪里使用了**特定的字段——或者它们是否根本没有被使用。

**[字段管理器](https://plugins.craftcms.com/field-manager)**
标准字段列表的另一个替代。然而，它没有显示字段在哪里被使用，只是如果它们是“孤立的”——根本不被使用。字段管理器还允许克隆、导出和导入字段。

**[Kint](https://plugins.craftcms.com/kint)**
Kint 是 PHP 应用程序的调试助手。它允许你在附加到网站甚至浏览器控制台的小工具中分析你的变量。这个插件可以让你在 Craft CMS 模板中使用 Kint。

**[队列管理器](https://plugins.craftcms.com/queue-manager)**
这个插件让调试卡住的队列任务变得更加容易。它显示详细的队列作业信息，并允许重试或取消整个队列。

**[播种机](https://plugins.craftcms.com/seeder)**
播种机生成用随机数据填充的参赛作品。非常适合测试填充各种内容时模板的行为。

**[CP 清除缓存](https://plugins.craftcms.com/cp-clearcache)**
在主控制面板菜单中增加“清除缓存”按钮。少一次点击。

**[环境标签](https://plugins.craftcms.com/environment-label)**
你在制作中有没有搞错环境，对网站做了不好的事情？这种事不会再发生了。这个插件在控制面板上添加了大红色的**环境标签**，它会根据网站所处的环境而显示(或不显示)——因此，同一网站的生产和开发或试运行实例可以清楚地区分。

**[元素图](https://plugins.craftcms.com/element-map)**
这个插件创建了一个链接列表，这些链接指向与控制面板中当前查看的元素有关系的元素——类似于条目、类别或资产。

**[日志](https://plugins.craftcms.com/logs)**
这个简单的插件让你使用控制面板浏览工艺日志。有助于快速检查远程服务器上网站的运行情况。