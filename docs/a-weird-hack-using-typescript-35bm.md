# 使用 TypeScript 的奇怪黑客行为

> 原文：<https://dev.to/sidvishnoi/a-weird-hack-using-typescript-35bm>

嗨！在我上一家公司的一个项目中，我遇到了现代 JavaScript 构建系统的一个奇怪问题，为了解决它，我写了一个更奇怪的黑客。

抱歉，如果你发现标题是点击诱饵。备选标题:在 TypeScript 中使用常量断言保持常量同步。

## 问题

该项目是使用 create-react-app (CRA)和 TypeScript 创建的，它也有一个 Express 服务器，也是在 TypeScript 中。项目结构很简单(这里展示了一个更加简化的版本)。

```
./
  src/
    App.tsx
  server/
    app.ts 
```

然后有一个常数。

```
const options = ["1 day", "1 week", "2 weeks", "1 month", "1 year"]; 
```

客户端需要常量`options`来显示`<select>`中允许的选项列表，服务器需要它来验证来自客户端的请求。现在使用 DRY 原则，我不应该在两个文件中写相同的`options`值——因为两个地方的值可能会不同步。所以，它需要在`src`和`server`之间共享——这就是问题的开始。

原来，create-react-app 不允许在`src`目录之外导入([见本栈溢出帖](https://stackoverflow.com/a/44115058))，所以我无法从`server`目录导入`options`。

嘿，希德，你为什么不从`src`进口呢？

```
// server/app.ts
import { options } from '../src/App.tsx'; 
```

`server`中的构建过程很简单。取一个`.ts`文件，通过`tsc`运行，创建一个`.js`文件，保持相同的目录结构。

所以，当我在我的`server/app.ts`上运行`tsc`，而不是创建一个目录结构，比如:

```
./server-build/
  app.js 
```

它最终创建了:

```
./server-build/
  server/
    app.js
  src/
    app.js 
```

哎呀。仅仅为了共享一个常量而打乱构建目录或构建过程？不酷。

## 钻营

前一天，我遇到了 [constant assertions](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html#const-assertions) ，一个新的 TypeScript 特性。

原来，在 create-react-app 中，我们不能导入`src`目录之外的值，但是我们可以导入类型。

```
// server/app.ts
// a const assertion
const options = ["1 day", "1 week", "2 weeks", "1 month", "1 year"] as const;
// export the type instead of value
export type Options = typeof options; 
```

```
// src/App.tsx
import { Options } from '../server/app.ts';
const options: Options = ["1 day", "1 week", "2 weeks", "1 month", "1 year"]; 
```

它是如何工作的？比方说，我将`server`中的“2 周”改为“3 周”。会发生什么？嗯，客户端编译失败！它失败了，因为它期望选项具有类型`Options`，但是“3 周”在索引 2 处的`Options`中不存在。如果我只在`src`中改变，效果也是一样的。

所以，我们用 TypeScript 同步了两个变量。不是很干，但它 works™️.

自己看吧:
[https://www.youtube.com/embed/NR6VQ0awdQs](https://www.youtube.com/embed/NR6VQ0awdQs)