# 🤖重复测试。重复测试。重复测试。

> 原文：<https://dev.to/ehmicky/repeat-tests-repeat-tests-repeat-tests-4l39>

[![](img/3a92c719a7509438da04c131579c9a32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--84luUfaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ehmicky/design/master/test-each/test-each.svg%3Fsanitize%3Dtrue)

[`test-each`](https://github.com/ehmicky/test-each) 是一个 Node.js 库，使用不同的输入重复测试([数据驱动测试](https://en.wikipedia.org/wiki/Data-driven_testing)):

*   独立测试运行程序:与您当前的设置兼容
*   为任何 JavaScript 类型(不仅仅是 JSON)生成描述性的、唯一的测试标题
*   在每个可能的输入组合上循环(笛卡尔乘积)
*   可以使用随机函数(模糊测试)
*   快照测试友好