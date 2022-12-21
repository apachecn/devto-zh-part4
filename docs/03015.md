# 您是创建模板还是自动化初始设置？

> 原文：<https://dev.to/darksmile92/do-you-create-templates-or-automate-initial-setup-15k3>

这个讨论涉及到自由职业者、雇员和兼职项目的所有者。

谁不知道:你辛辛苦苦地搭建开发环境，配置好一切，启动项目。后来在另一个项目中，你复制了前一个项目的全部或部分内容，修改了一些东西，对你忘记调整的错误和地方感到恼火，并以一个正在运行的第二个项目结束。

对于私人项目来说，这同样令人讨厌，但并不重要。然而，在专业的工作环境中，你应该避免这些不必要的时间。尤其是作为一个自由职业者，更是如此，因为这对送货时间和时薪有相当大的影响。

我自己的一个例子:
前段时间我用 Next、React 和 GraphQL (Prisma)设置了一个应用。
只是粗略的步骤:

*   将 Prisma 服务器部署到[数字海洋](https://digitalocean.com/) (Docker)
*   设置 MySQL 服务器(也是 Docker)
*   配置防火墙
*   在项目中正确设置环境变量
*   初始化 Git

这个架构非常适合我的下一个项目，所以我创建了一个`docker-compose.yml`文件来帮助部署 Prisma 服务器和 MySQL 服务器。
对于后端和前端部分，我复制了旧项目的文件夹，不得不(在搜索和替换的帮助下)费力地浏览所有配置，定制它们并更改其他地方(应用程序名称、常量等。).
这一切对我来说似乎很乏味。
最后，我也为这些任务写了一个简短的 shell 脚本。

然后今天早上我读了一篇关于 200 美元挑战的文章:

[![florinpop17 image](img/5c4e24038a466f1eb696220a47ecdbcc.png)](/florinpop17) [## 200 美元的挑战

### 弗罗林流行 9 月 10 日 194 分钟阅读

#freelancing #programming #challenge #money](/florinpop17/the-200-challenge-22mk)

我想:
作为一个自由职业者，你必须能够快速启动一个新项目，你需要不同类型的项目模板和最好的脚本来自动设置。

### 我到目前为止使用的是什么

*   在数字海洋上部署机器的脚本(通过 API)
*   在数字海洋上设置防火墙的脚本(通过 API)
*   我在第一个 React+GraphQL 教程的基础上创建了一个 temaplate，该教程来自于T3T5T7T9[韦斯博斯](/wesbos)<button class="crayons-btn follow-action-button  whitespace-nowrap" data-info="{&quot;id&quot;:28651,&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;}" data-follow-action-button="true">[我是一个全栈开发者](/wesbos)</button>(嘿韦斯，病得很厉害！👍)
*   各种 docker 为不同的拓扑结构编写文件
*   AWS Cloud9 部分作为 IDE，能够在任何地方编码，并且如果需要的话，能够与他人分享。

所以现在我很好奇...

#### ❓What 模板是你创建的吗？

#### ❓For 哪个书库？

#### 你用的是❓Do 脚本吗？如果是，他们会怎么做？(粗略)

#### ❓Do 你有本地 IDE 还是云中？(AWS C9，Codenvy，...)

#### ❓What 你造过其他聪明的帮手吗？