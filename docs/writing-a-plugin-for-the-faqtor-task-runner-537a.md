# 为 Faqtor 任务运行器编写插件

> 原文：<https://dev.to/algebraicbrain/writing-a-plugin-for-the-faqtor-task-runner-537a>

为 Faqtor 编写插件是一个简单明了的过程。示意性地看起来是这样的(类型脚本代码):

```
import * as faqtor from “faqtor”; 

export const createFactor = (....arguments): faqtor.IFactor => {

    const run = async (): Promise<Error> => {
        ....do some work here
    }

    return factor.func(run);
} 
```

让我们用 faqtor-of-rollup 插件的例子来检验这一点，顾名思义，它打算在我们的项目中使用 Rollup bundler。[继续阅读](https://medium.com/@bineev/writing-a-plugin-for-the-faqtor-task-runner-4c8e967546b9)