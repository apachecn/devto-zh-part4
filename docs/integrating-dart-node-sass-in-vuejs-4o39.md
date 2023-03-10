# 在 Vuejs 中集成 Dart/节点 Sass

> 原文：<https://dev.to/helleworld_/integrating-dart-node-sass-in-vuejs-4o39>

### 内容

⒈ [简介](#1)⒉[sass in a vue 项目从零开始](#2)t5】⒊[sass in a existing vue 项目](#3)
⒋[dart sass or node sass and why？](#4)
⒌ [牢记在心](#5)

#### 你好，用户们！

如你所知， **Ruby Sass 支持于今年**三月完成( *2019* )。这意味着每个人都应该将他们的版本迁移到 **Dart Sass 或 Node Sass** 。

在这篇文档中，我将向您展示如何**在一个已经存在的 Vuejs 项目中安装 Sass**以及如何**从头开始在一个 Vuejs 项目中集成 Sass**。

# 从头开始的 Vue 项目中的 Sass

这是比较容易的部分，为什么？因为有了 **Vue-CLI 3** 就可以自动集成 SASS 了！

让我们编码💻。

首先我们会**安装最新的 Vue Cli 版本**。我们将打开项目文件夹中的终端，输入

```
npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

当这个完成后(可能需要一些时间)，我们将开始**创建我们的 Vue 项目**。我们将键入

```
vue create your-project-name 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经到了有趣的一点。

如果你曾经用 Vue Cli 创建过一个 Vue 项目，你应该已经知道这是怎么回事了。如果你没有，那么*欢迎*来到你的第一次。

我们将选择

```
> Manually select features 
```

Enter fullscreen mode Exit fullscreen mode

继续选择您希望在项目中包含的每个特性。**确保选择 CSS 预处理选项**。我也会选 Linter:

```
> CSS Pre-processors
> Linter / Formatter 
```

Enter fullscreen mode Exit fullscreen mode

下一步。这是本文的**重点**。在列表中显示的所有选项中，您可以选择 Dart Sass 或 Node Sass，我会选择...

```
> Sass/SCSS (with dart-sass) 
```

Enter fullscreen mode Exit fullscreen mode

下一步，选择你喜欢的棉绒和你喜欢的选项。我的是:更漂亮，在保存和专用配置文件中。

搞定了。当安装完成后，回到你的项目文件夹，输入

```
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

如果您在文本编辑器中检查您的 **App.vue** 代码，您将会看到您的< *风格* >标签现在使用的是萨斯/SCSS:

```
<style lang=”scss”>
// Your AWESOME styles go here
</style> 
```

Enter fullscreen mode Exit fullscreen mode

**✨AWESOME✨** ，你说呢？

去吧，**是时候让你的时髦风格大放异彩了**！

# 现有 Vue 项目中的 Sass

如果您有一个项目**不是使用 Vue Cli 创建的，或者您只是忘记选择预处理器选项**，那么您最好安装 Node Sass+Sass Loader。只要在项目文件夹中打开你的终端，输入

```
npm install --save-dev node-sass sass-loader 
```

Enter fullscreen mode Exit fullscreen mode

现在去你的。vue 组件，搜索你的< *style* >标签，通过添加 *lang="scss"* :
告诉他现在在用 **SASS/SCSS** 说话

```
<style lang=”scss”>
// Your AWESOME styles go here
</style> 
```

Enter fullscreen mode Exit fullscreen mode

# Dart Sass 或 Node Sass，为什么？

Dart 和 Node Sass 的**区别**是什么？好吧，让我们作为*的旁注*。

Dart Sass 可能运行**更慢，使用更多内存**(它编译纯 JavaScript)，然而，**它比 Node-Sass 更适合跨平台**。另外，Node-Sass 文件需要花费**太长时间来安装**，这与小而简单的原型或项目不匹配。您可以在此了解更多信息:

[讨论 1](https://stackoverflow.com/questions/56150402/vue-cli-css-pre-processor-option-dart-sass-vs-node-sass)
[讨论 2](https://github.com/JeffreyWay/laravel-mix/issues/1832)

# 记住

📌你必须跑

```
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

为了现场观看你的 SASS 变化。不要忘记这一点，否则你将改变你的风格，而不会在你的页面上看到它们的变化！

📌记住在运行你的 *npm run serve* 之后，你会在你的终端**中看到你的应用在哪里运行*本地*，例如 *localhost:8080* ，<u>而不是你的 index.html 页面</u>在项目里面。**

## 临终遗言

这篇文章将是我将要上传的一个方便的 Sass 资源集的一部分。

长命百岁，继续编码，回头见💻！