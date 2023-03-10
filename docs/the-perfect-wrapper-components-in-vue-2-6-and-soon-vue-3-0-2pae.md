# Vue 2.6 和即将推出的 Vue 3.0 中的完美包装组件

> 原文：<https://dev.to/veebuv/the-perfect-wrapper-components-in-vue-2-6-and-soon-vue-3-0-2pae>

你是想建立一个看起来像青少年卧室的代码库，还是想建立一个看起来像直接来自宜家的代码库。

干净、可预测和快速的代码与不一致、错误百出、需要几个世纪才能添加功能的代码之间的区别归结为几个组件。

我们今天讨论的组件是以设计系统组件的形式出现的，主要是包装器组件。作为一名 Vue /前端开发人员——你需要和你的设计师伙伴一起工作，确保正在构建的设计系统简洁且“可继承”。

可遗传？是的——可继承的，也就是说，设计模式建立在自身之上，不断进化的设计，而不是看起来属于不同项目的组件——这正是我们如何构建[https://flowyse.com](https://flowyse.com)

好了，说够了，我们如何构建一个强大的包装组件结构？让我们在示例中使用一个输入字段和一个按钮。

第一步:

解决方案架构问题:

*   输入字段有哪些状态？
*   数据被提取和发送到哪里
*   它是维护自己的数据层还是从父数据层吸取数据
*   我是否将所有输入字段直接同步到 Vuex 这样的中央管理存储？

第二步:
答案

*   悬停、聚焦、错误
*   父组件
*   不会，数据层来自父层(在这种情况下，您可以按照自己的方式进行)
*   不，我们正在将所有数据同步到输入的父项。好了，现在已经完成了，让我们来看一个非常简单的输入组件。
*   我们有一个包装输入组件的根标签
*   我们将输入组件绑定到$attrs(很快会有更多相关内容)
*   我们听“$听众”(不管那是什么)
*   最后，我们监听“输入”的变化，并向父节点发出“变化”事件

[![](img/bd51302406050a212cc2730e3b0b933f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bj6xQoDd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/633/1%2AUxRUq2BQdflM8tRmYH2Y9w.png)

VueJS 知道包装组件是理想的，是每个组件驱动框架的自然属性。“道具”命题的一部分是，从父母那里传下来的、不被孩子认为是道具的属性或“指令”会自动附着在孩子的根上……令人困惑

[![](img/04de10791b19eb59e4679d846e20bf0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VvJRFWal--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/251/1%2An-cq13-yYyyjoNeBpiErrg.png)

想象一下将 greeting 传递到输入包装器中，如果 greeting 没有注册为一个属性，它将作为一个字段添加到我们的输入字段的包装器中，通过将 inheritAttrs 设置为 false，我们可以防止这种情况，而是覆盖它，并将所有元数据直接传递到输入组件中。

这样，您不需要在输入包装器中注册每个属性，但本质上它会自然地通过包装器传递自身(正如包装器应该允许的那样)

其次，我们有一个叫做$listeners 的东西——这是 Vue 的一个很好的方式，基本上是将组件监听的所有事件冒泡到父组件，这样你就不需要手动注册每个事件——这也是创建包装组件的一个很好的方式。

包装器的目标本质上是提供一些设计一致性——PS 包装器也应该 100%负责定制逻辑，您可以根据需要轻松地向包装器组件添加尽可能多的定制逻辑，您可以通过将值注册为属性来访问输入字段的值。

[![](img/9bdc7821c1039abd2ab02e11f59dde59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W5EKHVPB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/273/1%2AiSMGKl5PxXi1f9NG5alcNg.png)

我们剩下的是一个输入包装器，其模型定义在创建包装器的组件范围内，应该直接传递给输入组件的属性将按预期进行注册。

这正是整个 Flowyse 项目的构建方式，也是我能在 5.5 周内完成的原因之一。

### 🚨哇哇——有大事发生了🚨

有了 Vue 3.0，当你创建你的基本包装组件时，你不需要太多的配置

我们的组件变得更容易使用

[![](img/84cad36ba243ea556def2c1f7f011c5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EUSrYzzY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/552/1%2AtsiE4bQDN-shy1s8Lefosg.png)

请注意，我们不再有$listeners 或 inheritAttrs

在 Vue 3.0 中，不再有属性的自动继承，这意味着$attrs 自动包括所有非属性相关的属性，而不需要定义 inheritAttrs: false。

即使使用侦听器，v-on 也会直接编译成@enter 编译成 on-enter 的属性。通过简单地执行 v-bind="attrs "。

“attrs”还包括所有未发出的侦听器，最好的部分是…

通过执行 v-bind = " $ attrs ", v-model 编译成 model-value 和 on-model-update，因此不再需要模型选项，也不需要像以前那样覆盖原生输入事件