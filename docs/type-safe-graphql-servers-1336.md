# 类型安全的 GraphQL 服务器🔎

> 原文：<https://dev.to/swcarlosrj/type-safe-graphql-servers-1336>

这篇文章展示了在 GraphQL 服务器中添加类型安全的动机和步骤🎆

[![](img/df354cb1bb6bb96abf15b0fcff5389f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VP4wJH0S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/1%2ARu_buRxF7Cyk_RdJ5frVSg.png)

## TL；速度三角形定位法(dead reckoning)

> # [Buy back [T6】 API 【T7] documents & everything](https://codesandbox.io/s/yv004pqnq9)

[https://codesandbox.io/embed/yv004pqnq9](https://codesandbox.io/embed/yv004pqnq9)

### 为什么我要在我的代码库中添加类型安全？

类型使开发人员能够在开发 JavaScript 项目时使用高效的开发工具和实践，如**静态**检查和代码重构。

构建大型应用程序时，静态类型检查器可以帮助保护您的程序，并帮助您消除现有的错误(打字错误、拼写错误等)。TypeScript 和 Flow 都是限制 JavaScript 代码库中技术债务的好方法，因为人们，

> # JavaScript 很难🔥

同时，它可能是最好的编程语言，*开始，*因为它不需要安装和配置，但这并不意味着它真的**难**掌握和明确，**容易出错**！

JavaScript 被**弱**类型化(除了被*动态*类型化)会带来很多问题。注意，一种语言要么是静态类型的，要么是动态类型的，让我们比较一下它们之间的区别。

### 静态💥动态类型

**静态**
如果变量的类型在**编译时**已知，那么语言就是**静态类型化的**。对于某些语言，这意味着你作为程序员必须指定每个变量的类型(例如:Java，C，C++)。

**动态**
如果一种语言的类型与**运行时**值相关联，那么这种语言就是**动态类型化的**。这意味着你作为程序员可以写得更快一点，因为你不必每次都指定类型(例如:JavaScript、Python、Perl)

> # Remember
> 
> # Static compile-time Java language (a computer language, especially used to create websites)
> 
> # Dynamic run-time Java Script language

说到这些语言，为了阐明它们的相似之处，我不得不提到我最喜欢的 JavaScript 引言之一，它出自《你不知道的 JS》的作者之手💯推荐阅读):

> " JavaScript 与 Java 的关系就像嘉年华与汽车的关系一样."凯尔辛普森

毫无疑问，他们看起来不像。让我们把焦点放在最流行的动态类型语言上…

[![Your 😑when you’re trying for 1st time JS coming from statically typed languages](img/47b663ecb7e2a8e6344aa769bcb68c82.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--tIZJXKdj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AyerQrnhxecQaJNQ1EIrXmg.png) *贵😑当你第一次尝试来自静态类型语言的 JS 时*

请注意，一种语言可以被动态类型化，也可以被强类型化，从而产生一种更好的语言。我们如何用 JavaScript 做到这一点？

## JavaScript 中的类型安全

让我们回顾一下将静态类型添加到我们的 JS 代码库中的最著名的工具！

### 打字稿

***JavaScript 的类型化超集*** 可在任何浏览器、任何主机&任何操作系统上编译成普通 JavaScript。
[**微软/TypeScript**
*TypeScript 是 JavaScript 的一个超集，编译后清理 JavaScript 输出。-Microsoft/TypeScript * github . com](https://github.com/Microsoft/TypeScript)

**优点&缺点**
👌社区&工装
👍亚秒级响应时间
👎学习曲线

### 流量

静态 ***类型检查器*** 用于您的 JavaScript 代码。它做了大量的工作来使你更有生产力，使你更快、更聪明、更自信地编码，并且达到更大的规模。
[**脸书/Flow**
*在 JavaScript 中加入静态类型，提高开发者生产力和代码质量。——脸书/Flow*github.com](https://github.com/facebook/flow)

**优点&缺点**
👌现成的实用程序
👍零配置
👎更慢更笨重

### 打字稿 vs 流

无论你使用的是 TypeScript 还是 Flow，都要注意* *你写的不是普通的 JavaScript。**TypeScript 实现了*类型检查器*和发出普通 JavaScript 的*传输器*。Flow 只做*类型检查*，并依靠 [*Babel](https://babeljs.io/) *(或其他工具)移除类型注释。

如果你想了解更多关于语法和可用性的区别，我强烈推荐你阅读[这个比较](https://github.com/niieani/typescript-vs-flowtype)！

在这篇文章中，我们将使用 [**TypeScript](https://github.com/Microsoft/TypeScript) **在我们的例子中，它对我们稍后需要的类型生成有更好的支持。

## 图 QL 作为 API

众所周知，GraphQL 是构建 API 的强类型规范，它有很多好处，我们应该好好利用。

无论您是遵循*模式优先*还是*解析器优先*的方法，您都希望最终拥有* *单一的事实来源* *来开发*端到端类型安全的应用*。

避免手动静态输入，因为它太费时间，而且很难在不同版本中保持同步，我们可能希望**基于*单一来源的事实*自动生成**我们所有的类型。

在本文中，我们将使用**模式优先**方法，因此静态类型将基于我们的*GraphQL 模式*而不是从我们的解析器生成！

为了消除类型定义之间的冲突，我们可以引入自动化。这就是 **GraphQL 类型生成器**的用武之地！

## GraphQL 类型生成器

让我们回顾一下基于 GraphQL 模式的最常见的自动生成类型的工具。

### GraphQL 代码生成器

这个工具可以为你的 GraphQL 模式生成不同类型的代码输出，并记录你是在开发客户端、服务器还是数据库。
[【graphql-code-generator.com】**GraphQL 代码生成器*用一个函数调用从你的 graph QL 模式生成代码***](https://graphql-code-generator.com/) 

**优点&缺点**
👌客户端、服务器&数据库支持
👍文档&社区
👎早期发展阶段

### **GraphQLGen**

基于 TypeScript、Flow & Reason 中的 GraphQL 模式生成& scaffold 类型安全解析器。


 ****优点&缺点**
👌默认解析器生成
👍可与 Prisma 整合
👎仅服务器支持

### **【graph QL code generator vs graph qlgen】**

两者都非常适合生成*类型脚本* & *流*类型，尽管 **GraphQLGen** 不提供为客户端生成它们的能力。相反， **GraphQL 代码生成器**可以，它也为你提供了大量的插件/配置&来创建你自定义的类型生成模板。我发现这真的很有用，可以按照你的意愿配置一个模块化的 GraphQL 模式！

在这篇文章中，我们将使用 [**GraphQL 代码生成器](https://graphql-code-generator.com)* *在我们的例子中，它有更好的定制，我们稍后将需要。

> # Okay, I know, there are too many theories …

[![](img/4b01d5cb421cd9b28890e956106da4a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pc9LaCKx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AlBgPeLa4sfAqKw8Bj9OpKA.gif)

> # … Let's set

## 设置[排版](https://github.com/Microsoft/TypeScript)

[![[TypeScript](https://typescriptlang.org) main features](img/1548a062cf2e38e41a44eaabc025e95e.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Uts5kvgB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2552/1%2AF9SdgWKARE5TFVwZQ2JZSw.png) * [打字稿](https://typescriptlang.org)主要特点*

### **安装它**

```
yarn add -D typescript 
```

### **安装运行库**

为了用 Node 执行 TypeScript，您必须添加以下工具:

[* TS-Node](https://github.com/TypeStrong/ts-node)*:Node . js 的 TypeScript 执行和 REPL，有源码图支持。
[*Nodemon](https://github.com/remy/nodemon)* :监控 Node.js 应用程序中的任何变化，自动重启服务器。

```
yarn add -D ts-node nodemon 
```

你可能想使用其他配置，设置更适合你的应用程序！

### 包含@类型

添加您当前在代码库上使用的库的*高质量*类型定义，如 graphql、lodash 等。如果你是🤨关于这个，查一下[*确定类型的文档](http://definitelytyped.org/) *！

```
yarn add -D @types/graphql 
```

### **创建打字稿配置**

这样，TypeScript 将包含所有的。ts 文件作为编译上下文的一部分。它还会选择一些默认的编译器选项，你可以在这里查看！

```
**// tsconfig.json
**{
  "compilerOptions": {
    "target": "esnext",
    "module": "commonjs",
    "moduleResolution": "node",
    "sourceMap": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts"]
} 
```

### **创建运行配置**

*Nodemon* 支持位于当前工作目录中的本地配置文件，在那里你必须指定诸如哪个**路径来监视** &命令在任何重新加载后执行，这就是你的服务器。检查[样本配置](https://github.com/remy/nodemon/blob/master/doc/sample-nodemon.md)！

```
**// nodemon.json
**{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["src/**/*.spec.ts", "src/types/**/*.d.ts"],
  "exec": "ts-node ./src/index.ts"
} 
```

### **添加运行配置**

```
**// package.json
**{
  ...
  "scripts": {
    ...
    "start": "nodemon",
    ...
  }
  ...
} 
```

## **改名。js 文件👉。ts**

将所有 JavaScript 文件扩展名修改为。ts，它将允许您利用所有的类型脚本功能

### 测试你的应用

运行你的 *GraphQL 服务器*，由于 JavaScript 和 TypeScript 是 100%兼容的(不是相反，JS 编译器上不允许 TS 类型)，应该可以用！

```
yarn start 
```

### 剔除错误

转换后得到错误消息并不意外。重要的是实际上一个接一个地检查这些，并决定如何处理错误。通常这些都是合理的错误，但是有时你必须解释你试图做的比 TypeScript 更好的事情。请不要犹豫，查看本[迁移指南](http://www.typescriptlang.org/docs/handbook/migrating-from-javascript.html)以帮助修复可能的错误！

## **设置 [GraphQL 代码生成器](https://graphql-code-generator.com)T5】**

[![[GraphQL Code Generator](https://graphql-code-generator.com) main features](img/21b9cb17e192d635c374d0702e36a189.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--mO4nR1Zx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2876/1%2AJw_S5yw_Jsi7cMIXXD8B4Q.png) * [ GraphQL 代码生成器](https://graphql-code-generator.com)主要特性*

### **安装它**

```
yarn add -D graphql-code-generator 
```

### **安装插件**

我们将设置*公共*，*服务器*&* resolvers TypeScript *插件，你可能想使用其他的，查看可用的[插件](https://graphql-code-generator.com/docs/plugins/)！

```
yarn add -D graphql-codegen-typescript-common graphql-codegen-typescript-server graphql-codegen-typescript-resolvers 
```

### 创建上下文类型

如果您希望为 GraphQL *上下文*和* *使用自定义类型，并且不希望在每次声明解析器时都指定它，您可以手动创建它，并将其添加到*运行配置*文件中(我们将在下一步中这样做):

```
**// types/context.d.ts
**export type MyContext = {
  db: any;
  find: any;
  limit: any;
  order: any;
  project: any;
  sort: any;
}; 
```

**创建运行配置**

CLI 将自动检测已定义的配置文件，并相应地生成代码，您可以在这里检查可用选项！

```
**// codegen.yml***
*schema: src/schema/**/*.ts
overwrite: true
watch: true
require:
  - ts-node/register
generates:
  ./src/types/types.d.ts:
    config:
      contextType: ./context#MyContext
    plugins:
      - typescript-common
      - typescript-server
      - typescript-resolvers 
```

### 添加运行配置

```
**// package.json
**{
  ...
  "scripts": {
    ...
    "generate": "gql-gen",
    ...
  }
  ...
} 
```

## 生成类型⚙️

一切都是为了得到我们想要的静态类型😍，让我们实现它吧！

```
yarn generate 
```

## 添加解析器类型！

现在，您只需将* TypeScript *生成的静态类型包含到*GraphQL 解析器*中，如下所示

[![Adding TypeScript types into GraphQL Resolvers & 🔎the Resolvers Data Types](img/2a7e14d1428bb882812369b78e628cec.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--GyyPwce1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AYDeNWttazokA3eh4sYhwHg.gif) *向 GraphQL 解析器中添加打字稿类型&🔎解析器数据类型*

[![It’s not 😂, take a look to your types.d.ts generated!](img/d4ecabe9355791ac5f9058ba9b2b20dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DhPJEAqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AmPUc2fU1VPbW6gjbw1DjeQ.gif)T3】这不😂，看看你的 type . d . t 生成的！

## 加盖

为了使用*模式优先*方法将*类型安全*添加到您的 GraphQL 服务器中，您可以:

⚫安装 TypeScript
↳安装 TypeScript，运行库& @types
↳创建 TypeScript 配置&运行配置
↳添加运行配置
⚫️重命名。射流研究…👉。ts 文件
⚫️安装 GraphQL 代码生成器
↳安装 GraphQL 代码生成器&插件
↳创建上下文类型&运行配置
↳添加运行配置
⚫️生成类型⚙️
⚫️添加解析器类型！

## 👀好处

发现一些结合使用*TypeScript *和*GraphQL* 的使用案例！

### 进化你的图式

> ![unknown tweet media content](img/a88e5d42266dab2fb7fca5b1f978b5b6.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/Dv1mR8fXQAEHeRv.mp4" type="video/mp4"></video>![Carlos Rufo profile image](img/1d119cd95d32914c89080fc8721e353d.png)卡洛斯鲁福[@ swcarlosrj](https://dev.to/swcarlosrj)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)进化你的图式而不用担心变化，做一个叛逆者🤘，拉你所有的新提交！基于您的 [#GraphQL](https://twitter.com/hashtag/GraphQL) 类型定义&解析器生成的 [#TypeScript](https://twitter.com/hashtag/TypeScript) 类型将🔎对你来说所有可能的错误！2019 年 1 月 16:23PM-01[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1080137523190468608)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1080137523190468608)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1080137523190468608)3

### 🔎争论

> ![unknown tweet media content](img/760b13b866f5f8bb502f5692799b3bae.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/Dv1moipWkAA4595.mp4" type="video/mp4"></video>![Carlos Rufo profile image](img/1d119cd95d32914c89080fc8721e353d.png)卡洛斯鲁福[@ swcarlosrj](https://dev.to/swcarlosrj)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)别浪费你的⏳查看您自己记录的 API，不要离开 IDE🙌，不要离开 [@vscode](https://twitter.com/vscode) 。使用生成的 [#TypeScript](https://twitter.com/hashtag/TypeScript) 解析器类型探索所有的 [#GraphQL](https://twitter.com/hashtag/GraphQL) 参数！2019 年 1 月 16:23PM-01[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1080137527439314944)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1080137527439314944)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1080137527439314944)1

### 捕捉拼写错误

> ![unknown tweet media content](img/885851fc645564b1594ce06a41864ea8.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/Dv1nfUKXcAMK-5h.mp4" type="video/mp4"></video>![Carlos Rufo profile image](img/1d119cd95d32914c89080fc8721e353d.png)卡洛斯·鲁福[@ swcarlosrj](https://dev.to/swcarlosrj)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)“我刚刚花了大概 30 分钟的时间试图弄明白为什么我写的东西会出现在 catch 语句中，结果是我写错了` . toLowerCase()`🤦‍♀️" by[@ nikkitaftw](https://twitter.com/NikkitaFTW)
> 
> 即使伟大的开发者也会拼错， 避免他们在你的 [#GraphQL](https://twitter.com/hashtag/GraphQL) 解析器中使用 [#TypeScript](https://twitter.com/hashtag/TypeScript) 🚀2019 年 1 月 16:23PM-01[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1080137530320797696)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1080137530320797696)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1080137530320797696)2

### 🔎背景

> ![unknown tweet media content](img/75c99fb55a1f276cc10b98005f17e223.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/Dv1ns7DXcAAgv_y.mp4" type="video/mp4"></video>![Carlos Rufo profile image](img/1d119cd95d32914c89080fc8721e353d.png)卡洛斯鲁福[@ swcarlosrj](https://dev.to/swcarlosrj)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)如果你不知道，干♻️，用上下文！在您的 [#GraphQL](https://twitter.com/hashtag/GraphQL) 解析器中访问您需要的函数。生成的 [#TypeScript](https://twitter.com/hashtag/TypeScript) 类型将帮助您了解哪些是可用的以及它们各自的类型✨16:23pm-01 2019 年 1 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1080137533319720962)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1080137533319720962)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1080137533319720962)1

### 发现您的数据

> ![unknown tweet media content](img/229579304688a88edb56fc45d27fc8e1.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/Dv1qJeqWoAMN8wH.mp4" type="video/mp4"></video>![Carlos Rufo profile image](img/1d119cd95d32914c89080fc8721e353d.png)卡洛斯鲁福[@ swcarlosrj](https://dev.to/swcarlosrj)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)不确定🤨关于解析器返回的数据？摧毁它，嘲笑它，改变它😲，由于基于您的 [#GraphQL](https://twitter.com/hashtag/GraphQL) 模式生成的 [#TypeScript](https://twitter.com/hashtag/TypeScript) 类型，您将能够这样做😍2019 年 1 月 16:23PM-01[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1080137536507465729)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1080137536507465729)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1080137536507465729)1

## 未来

为了讨论新时代会有什么😬，你不会错过 GraphQL 的创建动机，[丹·斯查费](https://dev.toundefined)完美地再现了它们👇

[https://www.youtube.com/embed/6NYC-k2Abow](https://www.youtube.com/embed/6NYC-k2Abow)

(计划或理论的)纲要💥
解析器第一种方法，类型💥类/装饰生成器，
可读性💥可组合性，

… 2019 年的目标是为 GraphQL 而忙碌😅，
看了关于它未来的第一印象，
它的精彩不亚于铺天盖地，
但是真的，不要错过💯线程！

> ![Johannes Schickling profile image](img/622b5b3509dbec6f202946be7d86c34d.png)约翰内斯·席克林[@席克林](https://dev.to/schickling)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)🔮2019 年 GraphQL 预测:解析器优先的服务器框架将开始取代当前流行的模式优先的方法。
> 
> 主要原因:
> ◆无代码冗余(即解析器和类型定义单一)
> ◆更好的代码重用&类型安全
> ◆上级 DX09:49AM-02 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1080400713770024960)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1080400713770024960)110[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1080400713770024960)510

[![](img/c5ce5801aec5c7d14617a1b50036d15b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3M8UvU2v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2Af6IMUipNmjSrEp9iEAX4sw.jpeg)

我会继续关注这个帖子🔜与各自的**类型安全 GraphQL 客户端**🔎帖子，敬请关注！

请考虑一下🙏🏻荷兰、contribut♻️ing 和沙尔💜该死的。**