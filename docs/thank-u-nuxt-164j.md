# 谢谢你，笨蛋

> 原文：<https://dev.to/azure/thank-u-nuxt-164j>

爱莉安娜·格兰德曾经说过，“一个人教会了我耐心。还有一个教我痛苦”。她说的是她前任的。今天我要和你谈谈 Nuxt 的。

Nuxt 的？努 text is？努 text ises？

Nuxt 到底是什么？

Nuxt 是“VueJS 框架”。他们的网站上是这么写的，他们为什么要撒谎。

很难确定 Nuxt 到底做了什么，因为它做了太多。它会根据您的文件夹结构自动设置路由。它会自动为静态网站做代码分割。这将自动导致某人写一篇过多使用“自动”一词的博客文章。

但它最广为人知的可能是用 Vue 进行服务器端渲染的方式。

## 服务器端 Vue

Vue 是一个 JavaScript 框架。这意味着您编写的代码在运行时在浏览器中执行。但是几年前，有人聪明的意识到我们可以用 Node 在服务器上执行 JavaScript，所以我们应该可以在服务器上运行和编译 JavaScript 框架。他们称之为“同构 JavaScript”。

我还是不知道那是什么意思。还好改名为“通用 JavaScript”。Vue 的主要价值主张是，它使开发人员能够构建这些“通用应用程序”。它的功能远不止于此，但这也是它的主要功能。有点像约翰·卡拉辛斯基是杰克·瑞恩，但在更真实的意义上，他是并将永远是吉姆·哈尔珀特。

如果没有看到实际的东西，很难理解通用 JavaScript，所以让我们构建一些没有意义的东西。

## 毫无意义的演示

我的示例应用程序是一个带有 XMLHTTPRequest (XHR)的页面，它从 JSON 占位符 API 中提取一些假图像。我期待比这更好的假图像，但我想你不能抱怨免费的。就像我嫂子租了一台 margharita 机器然后把它们弄得太弱了。"听着，谢谢你的免费酒，但是我很失望."

[![](img/f3fc14b66abebf7d9c27e4087582619b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3OIPN8IG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paper-attachments.dropbox.com/s_496ED3FD66519F80FCA77F29F9ACBA296B1F83F6199E18571DF68908AE88A4EA_1560537829635_image.png)

这是制作这个页面的全部代码。