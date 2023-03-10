# GitHub 操作 v2: JS 操作

> 原文：<https://dev.to/lannonbr/github-actions-v2-js-actions-3544>

*原载于[lannonbr.com](https://lannonbr.com/blog/2019-08-17-gh-actions-js-actions/)T3】*

[![GitHub Actions Branding](img/b3e6267f7294dee120e00b1835b46af6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UJkqvOXQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tnptd5gph3d6783mqwow.png)

GitHub 在 8 月 8 日宣布了一个新的 GitHub 动作测试版，包括 CI & CD 支持。我想了解一下新测试版中添加的各种新功能以及它们能提供的功能。

这篇文章将探讨新的“JavaScript 动作”，它允许你通过编写 JavaScript 来构建新的动作。

## 回购设置&动作. yml

我们可以通过创建一个存放动作的目录来开始创建一个 JS 动作。

```
mkdir my-cool-action 
```

接下来，需要生成一个文件来描述特定操作的一些元数据。创建一个名为`action.yml`的文件，并在其中填入以下内容:

```
name: My Cool Action
author: Benjamin Lannon
description: 'Action  that  does  some  cool  stuff!'
inputs:
  name:
    description: 'Name'
    required: false
    default: 'Benjamin'
runs:
  using: 'node12'
  main: 'main.js'
branding:
  color: 'green'
  icon: 'send' 
```

YAML 语法的细节可以在 GitHub 的文档中找到，这里是: [action.yml 语法文档](https://help.github.com/en/articles/metadata-syntax-for-github-actions)。正是这个文件让 GitHub 知道在开发时应该寻找什么样的文件。需要的主要东西是:

*   给你的动作命名。
*   对动作将要做的事情给出解释。
*   告诉你这是什么样的动作。它目前支持基于容器的动作的`docker`,或者基于 JS 的动作的`node12`。
*   `runs.main`:当在`runs.using`中使用`node12`时，这个参数定义了你的入口点在当前目录中的位置。

同样，一个可选但值得一看的字段是定义动作输入的`inputs`字段。所有的 ID 都将被转换成一个环境变量，如`INPUT_`，后跟大写的 ID(例如:`name`将作为`INPUT_NAME`)。

接下来，我们可以创建一个 main.js 文件来实际运行一些 js 作为动作的一部分。

```
// main.js

let name = process.env.INPUT_NAME

console.log(`Hello, ${name}`) 
```

这是一个非常基本的例子，它从动作中获取一个字段并注销到控制台，但是它证明了开始 JS 动作所需要做的只是一个 yaml 文件和一个 JS 文件。

## 插入到 GitHub 动作工作流文件中

接下来，我将创建一个使用此操作的工作流。我将这个 repo 推送到 GitHub，这样我们就可以准备创建工作流了。

接下来，我们要创建一个工作流文件。它将存在于`.github/workflows`目录中。

```
# .github/workflows/main.yml
name: Run JS Action
on: push
jobs:
  run-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: my-cool-action
        uses: ./
        with:
          name: 'Ben' 
```

沿着文件一步步往下，我们描述了工作流、何时执行以及执行什么

`name`给出该工作流的详细名称。

`on`描述将运行此操作的事件。这里我们选择了`push`,每当有新的提交被推送到回购时都会发生。在将启动工作流的文档中有一个很大的[事件触发器](https://help.github.com/en/articles/events-that-trigger-workflows)列表。

`jobs`然后定义我们需要在工作流中运行的一组作业。目前我们只想运行一个作业，所以我们创建一个条目，并给它一个 id`run-action`。

在这项工作中，我们使用`runs-on`字段定义我们想要运行它的环境。这里我们选择`ubuntu-latest`(当时是最新的 LTS，18.04)，但是如果我们愿意，我们也可以在`windows`或`macos`上运行它。你可以在[虚拟环境](https://help.github.com/en/articles/virtual-environments-for-github-actions)中学习你的行动。

接下来，每个作业都有一系列要运行的步骤。第一步是使用 GitHub 构建的一个名为 [actions/checkout](https://github.com/actions/checkout) 的动作将回购取下来并签入其中。每个步骤都有一个`uses`字段，告诉我们在哪里可以找到一个动作。这可以包括相同回购中的相对路径、所有者/回购字符串(包括标签或提交 sha)、docker 文件的路径，甚至注册表上的 docker 映像，以及所有这些的大量变体，如[操作文档](https://help.github.com/en/articles/workflow-syntax-for-github-actions#jobsjob_idstepsuses)中进一步定义的

我们的第二步是要用我们的行动。`uses`字段的相对路径从 repo 的根目录开始，假设我们将`action.yml`文件放在根目录中，这是 GitHub 查找 JS 动作的方式，我们将`uses`字段设置为`./`。同样，我们希望传递我们的输入，这可以通过使用`with`字段来实现，该字段是一系列与我们的`action.yml`文件中的`inputs`相匹配的条目。

有关工作流文件语法的其他信息，请访问文档中的[GitHub 动作的工作流语法](https://help.github.com/en/articles/workflow-syntax-for-github-actions)页面。

如果我们最终提交这个文件，它将触发一个`push`，这样就触发了工作流本身。如果你看到下面，GitHub 提供了一个用户界面，我们可以看到我们所有的工作正在运行，这样，我们建立的行动工作！

[![GitHub Actions Workflow run UI](img/31b3d3a9839838f27cd96b2e1ec0e827.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QyqVAGvh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ueb3dg0flxqek2ipn6hk.png)

## 跟进

这是用新的 GitHub 操作设置 JS 操作和创建工作流的介绍。在以后的帖子中，我将深入 GitHub Actions Beta 2 的其他新特性，以及我们可以用它们来创建什么。接下来，我将继续这篇文章，使用 actions 团队创建的一些库来创建与 GitHub 的 API 交互的更高级的动作，这些库使得编写动作更容易管理。敬请关注。