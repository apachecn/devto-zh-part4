# Yarn 工作空间:没有 Lerna 的 monorepo 管理应用程序和编码示例

> 原文：<https://dev.to/hugo__df/yarn-workspaces-monorepo-management-without-lerna-for-applications-and-coding-examples-3920>

> 如何使用 Yarn 工作空间管理应用程序/编码示例？

当您不需要 git 区分和版本控制工具时，不需要 Lerna。

此外，Yarn 工作区是一个非常好的轻量级工具，可以让简单的 Node.js monorepo 操作更快地启动和运行。

在[github.com/HugoDF/yarn-workspaces-simple-monorepo](https://github.com/HugoDF/yarn-workspaces-simple-monorepo)的单一回购示例

## 纱线工作区 vs Lerna

使用工作区的优点:Yarn 工作区是标准 Yarn 工具链的一部分(不需要下载额外的依赖项)。它的范围非常有限，并且对您的安装进行重复数据消除(即使它们更快)。这非常适合管理代码示例或应用程序的单一报表。

工作空间的缺点:如果你正在处理一个*包* (npm 模块，库)的单一报告，Lerna 提供了发布/测试只改变文件的工具。

注意:Lerna 和 Yarn 工作空间是*事实上*被设计成一起工作，只是在你的`lerna.json`中使用`"npmClient": "yarn"`。关于与 Lerna 的比较，请参见 [Yarn 工作空间文档。](https://yarnpkg.com/lang/en/docs/workspaces/#toc-how-does-it-compare-to-lerna)

## 设置纱线工作区

```
{
"private": true,
"workspaces": ["examples/*", "other-example"]
} 
```

目录结构如下所示:

图形 LR；r[root]；r ->示例；例->例-1；例->例-2；r ->其他-举例；

**注意**:每个工作区(包)需要有一个 package.json，带有唯一的`name`和有效的`version`。根 package.json 不需要，只需要有`"private": true`和`"workspaces": []`就可以了。

## 自举单报表

与 Lerna 等价的将包括一个`lerna bootstrap`，它在所有的包中运行`npm install`。

对于工作区，因为依赖关系是从根锁定的，所以您只需要在顶层执行一个`yarn`。

为了让工作区工作，您的“工作区”文件夹需要有一个 package.json，其中包含一个`name`和`version`。

## 使用`yarn workspace`和`yarn workspaces`命令管理您的 monorepo

### 运行单个包中的命令

要在 monorepo 的单个包中运行命令，请使用以下语法:

```
yarn workspace <package-name> <yarn-command> 
```

例如:

```
$ yarn workspace example-1 run test
yarn workspace v1.x.x
yarn run v1.x.x
$ node test.js
test from example 1
✨ Done in 0.23s.
✨ Done in 0.86s. 
```

或者

```
$ yarn workspace example-2 remove lodash.omit
yarn workspace v1.x.x
yarn remove v1.x.x
[1/2] 🗑 Removing module lodash.omit...
[2/2] 🔨 Regenerating lockfile and installing missing dependencies...
success Uninstalled packages.
✨ Done in 2.83s.
✨ Done in 3.58s. 
```

“包名”应该是在`name`键下的 package.json 中找到的值。

### 运行所有包中的命令

要在 monorepo 的每个包中运行命令，请使用以下语法:

```
yarn workspaces <yarn-command> 
```

例如:

```
$ yarn workspaces run test
yarn workspaces v1.x.x
yarn run v1.x.x
$ node test.js
test from example 1
✨ Done in 0.22s.
yarn run v1.x.x
$ node test.js
{ public: 'data' } 'Should not display "secret"'
✨ Done in 0.23s.
yarn run v1.x.x
$ echo "Other Example"
Other Example
✨ Done in 0.11s.
✨ Done in 2.15s. 
```

或者

```
$ yarn workspaces run add lodash.omit@latest
yarn workspaces v1.x.x
yarn add v1.x.x
[1/4] 🔍 Resolving packages...
[2/4] 🚚 Fetching packages...
[3/4] 🔗 Linking dependencies...
[4/4] 🔨 Building fresh packages...
success Saved 1 new dependency.
info Direct dependencies
info All dependencies
└─ lodash.omit@4.5.0
✨ Done in 3.31s.
yarn add v1.x.x
[1/4] 🔍 Resolving packages...
[2/4] 🚚 Fetching packages...
[3/4] 🔗 Linking dependencies...
[4/4] 🔨 Building fresh packages...
success Saved 1 new dependency.
info Direct dependencies
info All dependencies
└─ lodash.omit@4.5.0
✨ Done in 2.76s.
yarn add v1.x.x
[1/4] 🔍 Resolving packages...
[2/4] 🚚 Fetching packages...
[3/4] 🔗 Linking dependencies...
[4/4] 🔨 Building fresh packages...
success Saved 1 new dependency.
info Direct dependencies
info All dependencies
└─ lodash.omit@4.5.0
✨ Done in 2.63s.
✨ Done in 10.82s. 
```

在[github.com/HugoDF/yarn-workspaces-simple-monorepo](https://github.com/HugoDF/yarn-workspaces-simple-monorepo)的单一回购示例

[unsplash-logo
马丁·亚当斯](https://unsplash.com/@martinadams?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)