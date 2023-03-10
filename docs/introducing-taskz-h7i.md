# 介绍 taskz

> 原文：<https://dev.to/rap2hpoutre/introducing-taskz-h7i>

[Taskz](https://github.com/rap2hpoutre/taskz) 是 Node.js 的库，一个简单的顺序并行任务列表运行器，用于终端。

[![](img/0f958f7b8682b838d3c1104d94cb1cf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wdiopsTc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8b97q7yyfd7bpoftf0oi.gif)

## 入门

通过`npm i taskz`安装。在任何脚本文件中创建您的任务序列，然后运行它。

```
const taskz = require("taskz");

taskz([
  {
    text: "first task - sleeps for 200ms",
    task: async () => await new Promise(resolve => setTimeout(resolve, 200));
  },
  {
    text: "this task will fail",
    task: async () => {
      throw new Error("this task failed");
    }
  }
]).run(); 
```

换句话说，您必须创建一系列任务:

```
const myTasks = [
  { text: "task 1", task: () => { /* ... */ } },
  { text: "task 2", task: () => { /* ... */ } }
]; 
```

然后将其传递给`taskz`函数，并调用`run`来启动该过程:

```
taskz(myTasks).run(); 
```

您还可以并行运行任务:

```
taskz(myTasks, { parallel: true }).run(); 
```

其他特性:[子任务](https://github.com/rap2hpoutre/taskz/blob/master/README.md#subtasks)，[失败时停止](https://github.com/rap2hpoutre/taskz/blob/master/README.md#fail)，[在任务间传递上下文](https://github.com/rap2hpoutre/taskz/blob/master/README.md#use-context)，
[在执行过程中改变任务内的文本](https://github.com/rap2hpoutre/taskz/blob/master/README.md#change-text-within-a-task-during-execution)。

尽情享受吧:[链接到 github 库](https://github.com/rap2hpoutre/taskz)