# 在 IntelliJ IDEA 中调试 TypeScript (Node.js)

> 原文：<https://dev.to/yutro/debugging-typescript-node-js-in-intellij-idea-3e6d>

前段时间我开始在服务器端写 typescript。就在我遇到调试问题后，在网上搜索如何做后，我发现一些教程不起作用或不完整，所以我决定创建自己的教程。它正在工作:)并且包含您需要的一切:)所以让我们开始吧。

### 依赖关系:

*   以打字打的文件
*   ts 节点
*   ts config-路径

让我们安装它们:

`npm i -S typescript ts-node tsconfig-paths`

创建 index.ts 文件`touch index.ts`

现在我们需要为调试添加一些示例代码，我们将它添加到 index.ts 文件中:

`function examplefunc(num1:number, num2: number){
const a = num1;
const b = num2;
const c = a + b:
return c;
}`

在 ide 中创建调试监听器配置
[![debug listener config](img/b89a4d16f7816ce85f061b75cfc02b7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LjT1lbbq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dc3wdw7pqfsw1k3qi9kr.gif) 
创建配置- >附加到 Node.js/Chrome

为 index.ts
[![ide run config](img/742f5fd7f92abb727b38c69687b7b037.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iNs4CDQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jinubefea5bcj2pblj8q.gif) 创建运行配置

节点参数:`--inspect --require ts-node/register -r tsconfig-paths/register`

javascript 文件:`index.ts`

启动调试器监听器配置`debuggerListener`

在代码中删除断点并运行`debugts`配置

[![result](img/b381a6e1476614aca8bd4836c8c572eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6fogF5BK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9d1ut9yr9flki1ypjr4k.gif)

现在执行将在断点处停止，现在我们可以很容易地在 IntelliJ 中调试 ts 文件:)