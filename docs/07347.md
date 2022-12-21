# 我到底如何使用我的分叉 NPM 包？

> 原文：<https://dev.to/dannyaziz97/how-the-hell-do-i-use-my-forked-npm-package-4pei>

你可以通过`npm install github:[GITHUB_USERNAME]/[GITHUB_REPO]`来安装叉子

但是这个包不能开箱即用。为什么？

大多数情况下，包装的`/dist`放在`.gitignore`中。因此，您需要构建一个打包版本的包，以便您的项目可以使用它。

为此，有两种方法。只有一个对我有用。

### 方法 1(对我没用的那个🤷‍♀️)

在你的 package.json 中你添加了一个`postinstall`，它直接进入你的并运行`npm install`和`npm run build`T3】

```
 "scripts": {
    "postinstall": "cd node_modules/[PACKAGE_NAME] && npm install && npm run build"
  }, 
```

现在只要运行`npm install`，你的包*就会被*更新到你的分支。

#### 如果不行怎么办？

对于我正在测试的一个包，`npm install`运行得很好，但是如果这个包已经在 node_modules 中，构建过程将永远无法工作...

### 方法二(分支法)

这种方法要求您在 fork 上创建一个只用于安装的分支(希望直到 fork 的主节点被合并)

1.  创建新的分支:
    `git checkout -b useLocally`

2.  从`.gitignore`上取下`/dist`

3.  将`build`命令添加到`precommit` :

```
 "precommit": [
     "build"
   ], 
```

推送分支

```
git add *
git commit -m "COMMIT_MESSAGE_HERE"
git push origin useLocally 
```

现在将分支安装到您的项目中
只需在安装
`npm install github:[GITHUB_USERNAME]/[GITHUB_REPO]#[BRANCH_NAME]`时将#【分支名称】添加到 repo 的 URL 中

现在`/dist`将被安装，而无需对 master 上的`package.json`进行任何更改！