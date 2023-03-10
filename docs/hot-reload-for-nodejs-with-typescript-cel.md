# 使用 TypeScript 为 Nodejs 自动重新加载

> 原文：<https://dev.to/0xkoji/hot-reload-for-nodejs-with-typescript-cel>

自从我加入团队，团队使用 TypeScript，我就在去年 8 月开始使用 TypeScript。

我的同事推荐我看一个 youtube 视频。我忘了标题...我会把链接，如果我记得它。

视频是一种旧的，但它仍然有助于了解`TypeScript`的概况。当时我就想“哦，好吧，看起来很合法，也很不错。”

然后开始在项目中使用它，我马上就想，这到底是怎么回事？？？？？，你是认真的吗？？？？我不喜欢你，打字稿。

然而，因为我的工作，我正在使用它😂，但我已将其用于我的个人项目。我仍然需要研究 TS 通过`Terminal`对我说什么，但现在我有点喜欢它了。

在这篇文章中，我将向您展示如何使用 TypeScript 为 nodejs 项目热重载。

### 步骤 1 用 Typescript 创建一个 nodejs 项目。

微软有一个初学者工具包
[https://github.com/microsoft/TypeScript-Node-Starter](https://github.com/microsoft/TypeScript-Node-Starter)

如果你认为这太多了，你可以使用我的模板。
T1】https://github . com/koji/typescript/tree/master/node _ typescript

### 第二步安装 nodemon

```
$ npm install -g nodemon
$ yarn add global nodemon 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 3 安装 ts-node

```
$ npm i install -g ts-node # if you prefer npx, you don't need to install it globally
$ yarn add global ts-node 
```

Enter fullscreen mode Exit fullscreen mode

### 第四步创建 nodemon.json

我们需要创建 nodemon.json 文件，因为 nodemon 只与`.js`一起工作。

```
{
  "watch": ["src"],
  "ext": "ts",
  "exec": "ts-node ./src/index.ts"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第五步运行`nodemon`

```
$ nodemon 
```

Enter fullscreen mode Exit fullscreen mode

### 用 TypeScript 快乐编码！！！

[![Buy Me A Coffee](img/9f22f6ad2e501da6d0f1a821a8763aa5.png)](https://www.buymeacoffee.com/koji)