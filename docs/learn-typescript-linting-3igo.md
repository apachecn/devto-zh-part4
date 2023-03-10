# 学习打字稿林挺

> 原文：<https://dev.to/ethanarrowood/learn-typescript-linting-3igo>

> 这篇文章改编自我的个人博客[山顶](https://blog.matterhorn.dev)上的一个更详细的版本。建议您阅读原始的 [Learn TypeScript 林挺](https://blog.matterhorn.dev/posts/learn-typescript-linting-part-1)文章系列，因为它有更详细的细节、示例，甚至还有后续的知识库。

## ESLint

### 1。安装斯拉夫语&@ typescript-斯拉夫语

```
npm i -D eslint @typescript-eslint/{eslint-plugin,parser} 
```

Enter fullscreen mode Exit fullscreen mode

### 2。创建 eslint 配置文件

```
touch .eslintrc.json 
```

Enter fullscreen mode Exit fullscreen mode

将以下文本复制到新的`eslintrc`文件

```
{  "extends":  ["eslint:recommended",  "plugin:@typescript-eslint/recommended"],  "parser":  "@typescript-eslint/parser",  "plugins":  ["@typescript-eslint"],  "env":  {  "node":  true  },  "parserOptions":  {  "ecmaVersion":  5,  "sourceType":  "module"  },  "rules":  {}  } 
```

Enter fullscreen mode Exit fullscreen mode

### 3。添加 lint npm 脚本

在 package.json 中，添加一个新脚本:

```
{  "scripts":  {  "lint":  "eslint 'src/**/*.ts'"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 4。快跑！

运行新的林挺命令:

```
npm run lint 
```

Enter fullscreen mode Exit fullscreen mode

如果您想自动修复林挺问题，请传递`--fix`标志:

```
npm run lint -- --fix 
```

Enter fullscreen mode Exit fullscreen mode

### 5。祝贺🎉

干得好！您的 TypeScript 现在配置了 ESLint。

如果你有兴趣进一步配置 ESLint 并学习在`.eslintrc.json`中找到的一些特定属性，请查看山顶博客上的[第 1 部分](https://blog.matterhorn.dev/posts/learn-typescript-linting-part-1)的学习类型脚本林挺系列。这篇文章深入讨论了诸如指定环境& ecmaVersion、配置分号用法和缩进样式之类的规则等细节，并涵盖了将 JavaScript 项目转换为 TypeScript 时最常见的问题之一(`no-unused-vars`错误)。

接下来是关于将流行的 formatters Standard 和 Prettier 与 ESLint 集成的部分。

* * *

## 标准

本节将介绍如何使用流行的[标准](https://standardjs.com/)自以为是的格式化程序来安装和配置新的 ESLint。

关于标准库，我最喜欢分享的事情之一是他们对 [*我能配置规则 x*](https://standardjs.com/index.html#i-disagree-with-rule-x-can-you-change-it) 问题的回应:

> 不。标准的全部意义是通过避免关于代码风格的[自行车脱落](https://www.freebsd.org/doc/en/books/faq/misc.html#bikeshed-painting)来节省你的时间。网上有很多关于制表符和空格的争论。那将永远无法解决。这些争论只会分散完成工作的注意力。在一天结束的时候，你必须“随便挑点什么”，这就是标准的全部哲学——它是一堆明智的“随便挑点什么”的意见。希望用户看到为自己的观点辩护的价值。

### 1。安装标准

```
npm i -D eslint-config-standard eslint-plugin-{standard,promise,import,node} 
```

Enter fullscreen mode Exit fullscreen mode

### 2。修改 eslint 配置

在`.eslintrc.json`中，将`"standard"`添加到`"extends"`列表的末尾。

```
{  "extends":  ["eslint:recommended",  "plugin:@typescript-eslint/recommended",  "standard"],  } 
```

Enter fullscreen mode Exit fullscreen mode

### 3。运行并评估新的输出

使用`npm`运行`lint`命令。输出应该略有不同，因为现在已经配置了新的规则，合并了标准的自以为是的格式。记得使用`--fix`选项来自动修复某些问题。

* * *

## 漂亮些

这一节将介绍如何安装和设置非常流行的[beautiful](https://prettier.io/)库。

> 我建议使用标准或漂亮，但不要同时使用两者！

### 1。安装更漂亮

```
npm i -D prettier eslint-config-prettier eslint-plugin-prettier 
```

Enter fullscreen mode Exit fullscreen mode

### 2。更新 eslint 配置

首先将`"plugin:prettier/recommended"`添加到`"extends"`列表，然后将`"prettier"`添加到`"plugins"`列表。

```
{
- "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
+ "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended", "plugin:prettier/recommended"],
  "parser": "@typescript-eslint/parser",
- "plugins": ["@typescript-eslint"], + "plugins": ["@typescript-eslint", "prettier"],
  "env": { "node": true },
  "parserOptions": {
    "ecmaVersion": 5,
    "sourceType": "module"
  },
  "rules": {
    "no-console": "warn",
    "@typescript-eslint/indent": ["error", 2]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3。运行、评估和修复

就像前两节一样，使用 npm 脚本`lint`来尝试新的林挺规则。使用`--fix`标志自动修复错误。

* * *

🎉再次祝贺！现在，您的 TypeScript 项目不仅配置了 ESLint，还配置了一个时髦的格式化程序。

如果你想了解更多关于集成格式化器甚至配置更漂亮的特定规则的知识，请查看 Mountain Top 博客上的学习 TypeScript 林挺系列的第 2 部分。

* * *

感谢你阅读这篇文章；我希望你喜欢它！我很乐意在 Twitter ( [@ArrowoodTech](https://twitter.com/ArrowoodTech) )下面或上面的评论中谈论林挺和格式化者。