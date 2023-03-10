# 使用更漂亮的保持你的代码格式整洁

> 原文：<https://dev.to/danimal141/keep-your-code-format-clean-using-prettier-40>

我最近使用了 [`prettier`](https://github.com/prettier/prettier) 来保持我团队的代码格式干净，然后我觉得它工作得很好。

本帖介绍如何在团队中使用`prettier`。

## 我们开始一个什么样的项目？

作为一个例子，让我们考虑这样一种情况，我们将创建一个 git 存储库并编写大量 markdown 文档，任何人都可以对该存储库做出贡献。

我们当然希望保持代码格式的整洁和一致。

## 安装`prettier`

遵循[这个文档](https://prettier.io/docs/en/install.html)，我们可以安装`prettier`。

在这篇文章中，我使用`yarn`来安装它。
我们所要做的就是在项目根中执行`yarn add prettier --dev --exact`。

之后，我们会得到`package.json`和`yarn.lock`。

## 如何使用`prettier`

为了尝试使用`prettier`，让我们将`package.json`更新为:

```
{  "scripts":  {  "prettier":  "prettier *.md --write"  },  "devDependencies":  {  "prettier":  "xxx"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

请准备一份脏样品减价单，如:

```
$ touch test.md 
```

Enter fullscreen mode Exit fullscreen mode

```
# Test

## testtest

This is a test!
 - test1
- test2 
```

Enter fullscreen mode Exit fullscreen mode

那我们就调用`yarn prettier`。
之后，样本文件的格式将变得清晰，如:

```
# Test

## testtest

This is a test!
 - test1
- test2 
```

Enter fullscreen mode Exit fullscreen mode

在这篇文章中，我将使用默认配置，但如果你想配置，请参考[本文档](https://prettier.io/docs/en/configuration.html)。

### 在 CI 上使用`prettier`来验证代码格式

目前，为了保持代码格式的整洁，每个人都必须在修改文件时执行`yarn prettier`。我觉得很容易忘记。

要注意有些格式是很快无法预料的，让我们使用 CI。

首先，要检查代码格式，我们可以使用`prettier --check`。让我们给`package.json`添加另一个命令。

```
{  "scripts":  {  "prettier":  "prettier *.md --write",  "lint:prettier":  "prettier --check *.md"  },  "devDependencies":  {  "prettier":  "xxx"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

互相审查代码，我们基本上都是用 GitHub 之类的。然后，我们可以通过 CI(如 CircleCI)在每个 pull 请求中调用验证命令。

这是 CircleCI 配置示例:

```
$ mkdir -p .circleci & touch .circleci/config.yml 
```

Enter fullscreen mode Exit fullscreen mode

```
version: 2.1
jobs:
  build:
    docker:
      - image: circleci/node:10
    working_directory: ~/repo

    steps:
      - checkout
      - restore_cache:
          keys:
            - v1-dependencies-{{ checksum "yarn.lock" }}
            - v1-dependencies-

      - run: yarn install

      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "yarn.lock" }}

      - run:
          name: Prettier
          command: yarn lint:prettier 
```

Enter fullscreen mode Exit fullscreen mode

集成 GitHub 和 CircleCI 后，我们可以保证代码格式(如果某些格式是意外的，CI 就失败了)。

为项目做贡献，每个人都必须做。

## 总结

*   `prettier`非常方便易用。
*   在 CI 上使用`prettier`,我们将能够更健壮地保持代码的整洁。

## 参考文献

*   [https://github.com/prettier/prettier](https://github.com/prettier/prettier)
*   [https://prettier . io/docs/en/](https://prettier.io/docs/en/)