# 将 Pre-commit 挂钩配置为 Pre-commit 挂钩

> 原文：<https://dev.to/juliang/configuring-prettier-and-typescript-compiler-as-a-pre-commit-hook-44jh>

我们可以通过以下方式轻松改善我们的开发人员体验:

*   防止提交/推送损坏的代码。
*   在我们的代码评审中避免无意义的格式争论。

我们决定使用 [git 预提交钩子](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)来帮助防止“中断的”提交。

我们从一个现有的 TypeScript 项目开始，但是如果你想看的话，这里有一个[演示库](https://github.com/JulianG/precommit-hooks-demo)。

## 1。安装[漂亮器](https://www.npmjs.com/package/prettier)、[哈士奇](https://www.npmjs.com/package/husky)和[皮棉器](https://www.npmjs.com/package/lint-staged)

```
yarn add -D prettier husky lint-staged 
```

Enter fullscreen mode Exit fullscreen mode

这些在运行时都不是必需的，所以使用`-D`以便将依赖项添加到“devDependencies”中是很重要的。

## 2。配置更漂亮

我们需要创建两个文件:

### [T1。prettierrc:](#prettierrc)

```
{
  "printWidth": 120,
  "proseWrap": "preserve",
  "semi": false,
  "singleQuote": true,
  "useTabs": false,
  "tabWidth": 2,
  "arrowParens": "avoid",
  "trailingComma": "es5"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 。前置任务:

```
node_modules
build
dist
res
coverage 
```

Enter fullscreen mode Exit fullscreen mode

当然，你可以在[中以任何你喜欢的方式](https://prettier.io/docs/en/configuration.html)进行配置。

## 3。创建 lint 暂存的配置文件:。lintstagedrc:

```
{
  "**/*.+(js|jsx|css|less|scss|ts|tsx|md)": [
    "prettier --write",
    "git add"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

这被配置为运行得更漂亮，并覆盖任何与上述模式匹配的**暂存文件**，然后暂存新的更改(如果有)。

## 4。创建一个 husky 配置文件:。huskyrc:

```
{
  "hooks": {
    "pre-commit": "tsc && lint-staged"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是配置预提交挂钩。它将使用上面讨论的配置文件运行 **`tsc`** ，然后运行 **`lint-staged`** 。

## 5。成功！

现在，每当我尝试提交时，预提交挂钩就会运行。如果——由于某种原因——我的代码无法编译，我会得到一个错误，并有机会在提交前修复它。

我也不用担心代码格式化，因为 **`prettier`** 会在提交前格式化任何暂存文件。

## 演示资源库

我在 GitHub 上设置了一个非常基本的[库](https://github.com/JulianG/precommit-hooks-demo)作为演示。

* * *

西蒙·威尔克斯在 [Unsplash](https://unsplash.com/search/photos/stream) 上的照片