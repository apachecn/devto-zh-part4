# 在 web 服务中应用和组织环境变量

> 原文：<https://dev.to/mtmr0x/applying-and-organizing-environment-variables-in-software-ogm>

我一直致力于在复杂的后端服务中重新设计一组环境变量及其组织。我不得不在公司内外做大量研究。整个项目花了我几个星期的时间，获得的知识使我实现了一种组织环境变量和定义最佳实践的格式。

> 声明:这不是一篇试图说“正确方式”的文章。请大家评论一下自己的疑惑和知识，以丰富主题。

在本文中，我将介绍一些架构决策、组织和环境变量的命名模型，我可以应用于这一挑战。

# 映射和理解所有环境变量

这部分工作定义了你对问题的了解。你的知识越多，你就越准确。这项任务可能会占用你 70%以上的时间，但这是值得的。因为与执行相比，计划是廉价的。

你需要好的重构工具。Jetbrains 很擅长这个。你可以在这里看一些[的东西。VSCode 离伟大不远，在重构方面做得很好](https://www.jetbrains.com/help/webstorm/refactoring-source-code.html)。它看起来不像 Jetbrains 和其他基于文档的 ide 那么强大，但这只是从他们文档的角度来看。

我是 VIM 的忠实用户。人们告诉我要尽量利用现代 IDE，但我要分享一些关于 VIM 如何强大的东西:如果它可以由 IDE 完成，它可以在 VIM 中更快地完成(但大多数时候并不容易)。如果你使用`vim-fugitive`加`deoplete`作为插件(如果在 neovim 中就更好了)，你可以做如下事情:

```
map <leader>l :vsplit<cr>:Ggrep "<C-r><C-w>"<cr><Esc>:copen<cr> 
```

它获取光标下的单词，在所有文件中搜索所有这样的单词，并将其加载到 VIM `quickfix-window`。从那里，可能性真的很令人兴奋。

编辑够多了。“映射”的更多含义:

您应该获得每个环境变量的所有使用关系。让我们以使用变量激活一个特性为例:

```
COOL_FEATURE_ENABLED=0 
```

您应该能够映射使用它的函数和所有使用第一个函数的函数:

```
COOL_FEATURE_ENABLED is used by `cool_feature_status` function at `src/services/cool_feature.extension`;

`cool_feature_status` is used by:
  - `send_email` function at `src/email/send.extension`;
  - `healthcheck` function at `src/healthcheck.extension`;

and so on. 
```

有了这个图表，你将更好地理解你可能面临的副作用，并成功地重构和清理未使用的代码。

# 定义命名约定

> "一个环境变量必须尽可能的短，尽可能的长."-堆栈溢出的某个聪明人。

我们都知道，在我们的行业里，给事物命名真的很难。它必须是自我解释的，简短的，不要试图过于简短，不能与应用程序或范围内的其他上下文冲突，等等。绝对不是件容易的事。

但是高级上下文可能更容易一些。你需要意识到的第一件事是关于阅读，问你自己:“我怎样才能读一些东西，并且准确地知道它是关于什么的？”上下文很重要，所以上下文是环境变量名中的第一件事。请记住，人类配置应用程序，它必须是人类可读的。

我喜欢用一个小框架来为环境变量命名，它是`CONTEXT`、`SUB_CONTEXT`、`OPTIONAL_SPECIFICATION`:

*   `CONTEXT`:主上下文是使用的技术或配置第一值，例如:`REDIS`或`LOG`；

*   `SUB_CONTEXT`:如果你正在配置一个像 Redis 这样的数据库，你可能希望在环境变量中有用户名和密码，比如像`REDIS_USERNAME`或`POSTGRE_HOST`；

*   `OPTIONAL_SPECIFICATION`:当您需要标记、量化、指定或定义属于环境变量的状态时使用，如启用或禁用功能、设置数值以改变上下文行为等。其用法的一些例子是:`ENABLED`、`DISABLED`、`TIMEOUT`、`MAX_SIZE`、`MIN_SIZE`。

一些例子:

如果您想要配置外部电子邮件服务来向您的用户发送邮件:

`EMAIL_SERVICE_ENABLED`->-T1:`EMAIL`；`SUB_CONTEXT`:`SERVICE`；`OPTIONAL_SPECIFICATION`:`ENABLED`；

`EMAIL_SENDER_ADDRESS`->-T1:`EMAIL`；`SUB_CONTEXT`:`SENDER_ADDRESS`；`OPTIONAL_SPECIFICATION`:不适用；

`EMAIL_SENDER_NAME`->-T1:`EMAIL`；`SUB_CONTEXT`:`SENDER_NAME`；`OPTIONAL_SPECIFICATION`:不适用；

或者配置您的日志:

`LOG_VERBOSITY`->-T1:`LOG`；`SUB_CONTEXT`:`VERBOSITY`；`OPTIONAL_CONTEXT`:不适用；

`LOG_OUTPUT_FILE`->-T1:`LOG`；`SUB_CONTEXT`:`OUTPUT_FILE`；`OPTIONAL_CONTEXT`:不适用；

`LOG_OUTPUT_FILE_MAX_SIZE`->-T1:`LOG`；`SUB_CONTEXT`:`OUTPUT_FILE`；`OPTIONAL_CONTEXT`:`MAX_SIZE`；

# 区分配置和策略

打开一个环境变量文件并找到许多“自我解释”的项目是很常见的。环境(生产、试运行、开发)可能会有不同的需求，基于环境的决策是**策略**。这不是一条规则，但政策的价值中包含`OPTIONAL_SPECIFICATION`是很常见的。

策略用于定义服务器规范，如打印或第三方使用的日志级别。举个例子，您正在使用 Redis，您需要配置 Redis，但是对于生产环境，您在集群模式下使用 Redis，这会改变您如何配置客户端的一些内容。

在这个例子中，我们会有这样的东西:

```
# CONFIGURATIONS

# Redis
REDIS_HOST='localhost'
REDIS_PORT='6379'
REDIS_PASSWORD='super secure password'

# POLICIES

# Redis
# 0 for disable
# 1 for enable
REDIS_CLUSTER_MODE_ENABLED=1 
```

这样，您就能够更好地组织您的服务配置和策略。激活特性、为 API 定义一个调试区域或者在开发环境中打印敏感信息以方便调试，这些都是从所需的配置中更好地组织起来的方面。

环境变量文件没有保存成为一个巨大的文件。即使在小型服务中也不行。让它更容易阅读，更容易让人们参与到项目中来的最好方法是在开始时对它进行良好的定义、组织和记录。从一开始就计划工作是一个游戏规则的改变。它需要耐心和投入大量时间与人交谈和思考。这是值得的。

感谢您的阅读，如果您有任何评论或想聊聊，请在这里评论或发微博给我。我很乐意进一步讨论这个话题。