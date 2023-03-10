# 用更漂亮的和 ESLint 格式化和修复所有文件(递归)

> 原文：<https://dev.to/sophiabrandt/format-and-fix-all-files-with-prettier-and-eslint-recursively-1gld>

**问题:**你有一堆 JavaScript 和。你希望用 **[更漂亮的](https://prettier.io)** 和 **[ESLint](https://eslint.org/)** 解析的 jsx 文件。

为什么？

你希望确保 [ESLint 分析你的代码中有问题的模式](https://eslint.org/docs/about/)。

您希望有一致的格式，并且在*编写*代码时不用担心代码样式。

在这篇文章中，我将向你展示如何用*一个命令*递归地修复和格式化你所有的 JavaScript 文件。

## 安装软件包

你需要安装 [ESLint](https://eslint.org/) 、[prettle](https://prettier.io)和一些辅助工具。

```
yarn add --dev eslint babel-eslint eslint-config-prettier eslint-plugin-prettier prettier prettier-eslint-cli 
```

Enter fullscreen mode Exit fullscreen mode

**如果使用 Create-React-App，不要单独安装 ESLint**。 [Create-React-App](https://create-react-app.dev/docs/setting-up-your-editor) 搭载 ESLint。单独安装往往会出现问题。

**对于 React，使用:**

```
yarn add --dev eslint-config-prettier eslint-plugin-prettier eslint-plugin-react prettier prettier-eslint-cli 
```

Enter fullscreen mode Exit fullscreen mode

## 配置 ESLint，更漂亮

### ESLint

在您的项目目录中创建一个`.eslintrc.json`文件:

```
{  "parser":  "babel-eslint",  "parserOptions":  {  "sourceType":  "module",  "ecmaFeatures":  {  "modules":  true  }  },  "plugins":  ["prettier"],  "extends":  ["prettier"],  "rules":  {  "prettier/prettier":  "error"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

对于 React:

```
{  "env":  {  "browser":  true,  "es6":  true  },  "parser":  "babel-eslint",  "parserOptions":  {  "ecmaVersion":  8,  "sourceType":  "module",  "ecmaFeatures":  {  "jsx":  true,  "modules":  true  }  },  "plugins":  ["prettier",  "react"],  "extends":  ["prettier",  "eslint:recommended",  "plugin:react/recommended"],  "rules":  {  "prettier/prettier":  "error"  },  "settings":  {  "react":  {  "version":  "detetect"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 ESLint 文档中阅读更多关于[配置选项的信息。](https://eslint.org/docs/user-guide/configuring)

### 漂亮些

在项目根目录下创建一个`.prettierrc.json`文件。你可以从几个[选项中选择](https://prettier.io/docs/en/options.html)。

这些是为我工作的:

```
{  "trailingComma":  "es5",  "semi":  false,  "singleQuote":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

## 配置 package.json

在您的`package.json`文件中，添加一个新脚本:

```
//  ...  "scripts":  {  //  ...  "format":  "prettier-eslint --write \"{,!(node_modules)/**/}*.{js,jsx}\""  }  //  ... 
```

Enter fullscreen mode Exit fullscreen mode

`--write`标志告诉插件自己修改文件。否则，您只能将输出记录到您的终端。

下一部分是一个`glob`，告诉工具要解析什么。

1.  `{,!(node_modules)/**/}`:排除`node_modules`目录下的所有内容，定位所有目录和子目录下的所有文件(通过`**`)

2.  `*.{js,jsx}`:定位所有扩展名为`.js`和`.jsx`的文件

这种设置会忽略`node_modules`文件夹中的所有内容，并且*会格式化项目中的每个`.js`和`.jsx`文件。*

如果您想了解更多关于 glob 选项的信息，您应该查看以下两个链接:

*   我怎样才能告诉 beautiful 递归解析文件？
*   GitHub 问题-更漂亮:添加一个如何忽略 node_modules #1358 的 glob 示例

现在运行:

```
yarn run format 
```

Enter fullscreen mode Exit fullscreen mode

## 可选:预提交挂钩

这种设置非常适合使用[预提交钩子](https://prettier.io/docs/en/precommit.html)。钩子将在你提交或者把你的代码推到一个库之前运行。

最简单的方法就是用 **[哈士奇](https://github.com/typicode/husky)** :

```
yarn add --dev husky 
```

Enter fullscreen mode Exit fullscreen mode

将 husky 配置添加到`package.json` :

```
{  "husky":  {  "hooks":  {  "pre-push":  "yarn run format",  "...":  "..."  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 链接

*   [更漂亮的](https://prettier.io)及其[选项](https://prettier.io/docs/en/options.html)
*   [ESLint](https://eslint.org/) 及其[选项](https://eslint.org/docs/user-guide/configuring)
*   [哈士奇](https://github.com/typicode/husky)