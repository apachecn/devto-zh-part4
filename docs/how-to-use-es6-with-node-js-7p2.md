# 如何将 ES6+与 Node 配合使用？射流研究…

> 原文：<https://dev.to/aidanlovelace/how-to-use-es6-with-node-js-7p2>

我真的非常喜欢 ES6+ Javascript 的新特性，比如`async/await`、新的`class`语法、`let`和`const`(去你的`hoisting`！)、`arrow functions`、`destructuring`、新的`import`语法等等。这些功能非常有用。我认为`import`语法特别适合 Node。JS 感觉更像是为 Javascript 设计的。

好的。好的。我会继续——我怎么能使用 ES6+和 Node。JS？超级简单。

1.  `cd`到你的项目在你的终端
    [![](img/4683c3a19820678889084ecd60a1a9fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fa78TTrj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aidanlovelace.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-17-at-11.58.32-PM-1.png)

2.  跑`npm install --save-dev esm`
    [![](img/ff006864183001338b7b6d92dc590da8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M91lrPuC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aidanlovelace.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-18-at-12.02.32-AM-copy.png)

3.  用`node -r esm {THE FILE}`
    [![](img/e445e3521ace3ff2cdd81b14722c59bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YAN8-pO3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aidanlovelace.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-18-at-12.02.32-AM.png) 运行你的主 Javascript 文件

如果你用的是`nodemon`，可以只运行`nodemon -r esm {THE FILE}`。