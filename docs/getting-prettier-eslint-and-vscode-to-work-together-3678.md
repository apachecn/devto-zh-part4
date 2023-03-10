# 越来越漂亮，Eslint 和 Vscode 携手合作

> 原文：<https://dev.to/chgldev/getting-prettier-eslint-and-vscode-to-work-together-3678>

很长一段时间以来，我一直试图让 getting beautiful、Eslint 和 Vscode 一起工作，但从未找到令我满意的解决方案。现在，当我在日常工作中从事一个新的 React 项目时，我终于找到了一些时间让它工作。

## 等等什么是 Eslint 和 beautiful？

Eslint 是一个 javascript linter，可以帮助您找到语法或其他错误。Eslint 可以通过插入预定义的配置或自己完全配置来扩展。搭配 vscode 的插件，它可以在你输入的时候显示错误。

Prettier 是相当多语言的代码格式化程序，包括 javascript。您可以用它来自动或选择性地格式化代码。

## 先决条件

我假设您对 NPM 和 Vscode 有基本的了解，因为我不会在这里涉及它。您需要具备:

*   安装的 Vscode
*   NodeJS 和 NPM 已安装

## 安装 Vscode 插件

首先安装 Vscode 插件 [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 和[beauty-Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)并确保它们被启用。

## 安装依赖项

```
npm i -D eslint eslint-config-prettier eslint-plugin-prettier prettier 
```

Enter fullscreen mode Exit fullscreen mode

## 设置配置文件

在项目根目录下创建一个`.eslintrc`文件。

在这里，我们基本上告诉 Eslint:

*   从推荐的更漂亮的配置扩展
*   注册我们安装的漂亮插件
*   将更漂亮的错误显示为错误

```
{  "extends":  [  "plugin:prettier/recommended"  ],  "plugins":  ["prettier"],  "rules":  {  "prettier/prettier":  "error"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

注意:你的 eslint 配置可以包含更多的规则。我让它简单易懂。

接下来，在项目根目录下创建一个`.prettierrc`文件。

你可以调整这些设置，或者[添加你喜欢的新设置](https://prettier.io/docs/en/options.html):

```
{  "semi":  true,  "singleQuote":  true,  "tabWidth":  2,  "useTabs":  false  } 
```

Enter fullscreen mode Exit fullscreen mode

## 启用格式保存在 Vscode 中

查找`formatOnSave`并勾选复选框，或将此行添加到`settings.json` :

```
"editor.formatOnSave": true, 
```

Enter fullscreen mode Exit fullscreen mode

## 试试看

如果您像上面一样保存了`.prettierrc`文件，Vscode 现在应该:

*   将代码缩进两个空格
*   使用单引号而不是双引号
*   添加分号添加每一行的结尾

如果您遇到问题，请尝试重新启动 Vscode。