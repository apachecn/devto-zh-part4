# 如何在节点中创建和运行 cron 作业

> 原文：<https://dev.to/wchr/how-to-create-and-run-cron-jobs-in-node-4eh0>

如果你想在你的应用上自动完成某些任务，省去手动完成这些任务的麻烦，比如删除日志文件、自动发送电子邮件，或者将你的数据库数据备份到 MongoDB/SQL 转储文件中，我们可以说没有更多的`cron jobs`可以提供帮助。

## 什么是 cronjob？

cron 作业是一种按照设定的计划自动运行的任务，例如，在 0000 时，您可能在 CDN(内容交付网络)上有一个 cron 作业清除站点缓存。

在本教程结束时，您应该能够创建一个简单的节点 cron 作业。

# 先决条件

要开始学习本教程，您必须具备:

*   安装在开发计算机上的节点
*   安装纱线或 NPM
*   编写 es5 Javascript 的知识

# 入门

## 设置

为了运行我们的 cron 作业，我们需要设置我们的节点项目。

```
// Create our folder and change our directory to where it is created
$ mkdir cron_jobs_with_nodejs && cd cron_jobs_with_nodejs
// Initialize our package.json file
$ yarn init -y or npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建一个简单的 cron 作业，在一分钟后删除一个文件。让我们创建一个文件来存放我们的代码

```
$ touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

我们需要安装某些包，以确保我们有一个 cron 作业

```
$ npm install express node-cron fs 
```

Enter fullscreen mode Exit fullscreen mode

*   为了让我们的 cron 作业在设定的时间后运行，我们需要初始化一个保持活动的连接，当我们初始化一个`HTTP`服务器时，`express`库将提供这个连接。您仍然可以使用节点内置的`HTTP`库。> *注意*:我们需要一个正在运行的程序来运行我们的 cron 作业，因此需要一个 HTTP 连接。
*   这是使我们能够成功运行 cron 作业的库。
*   `fs`:帮助我们很好地访问`file-system`。它自带节点，所以不需要安装。

## 代码

无聊但重要的一步已经结束，现在是好东西，代码。用本地编辑器打开`index.js`。

首先，我们必须设置我们的整个 express 服务器

```
// Import needed libraries
const express = require("express");
const Cron = require("node-cron");
const fs = require("fs");

// Initialize express
const app = express();

// Set  our default port
app.set("port", process.env.PORT || 3456);

// Initialize our server
app.listen(app.get("port"), err => {
  // If something goes wrong when initializing the server
  if (err) console.log(`Server failure due to ${err.message}`);
  console.log(`Server running on port ${app.get("port")}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

运行我们的服务器。

```
$ node src/index.js 
```

Enter fullscreen mode Exit fullscreen mode

如果成功，您将在控制台/终端上看到类似的消息。

```
Server running on port 3456 
```

Enter fullscreen mode Exit fullscreen mode

其次，我们将编写一个简单的 cron 作业，在每分钟后控制台记录一条语句。

```
// Import needed libraries
const express = require("express");
const Cron = require("node-cron");
const fs = require("fs");

// Initialize express
const app = express();

// Set  our default port
app.set("port", process.env.PORT || 3456);

// Schedule a cron job task
Cron.schedule("* * * * * ", () => {
  console.log("> After every minute the cron job runs");
});

// Initialize our server
app.listen(app.get("port"), err => {
  // If something goes wrong when initializing the server
  if (err) console.log(`Server failure due to ${err.message}`);
  console.log(`Server running on port ${app.get("port")}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

您一定想知道作为`Cron.schedule`方法第一个参数的奇怪值。这个值就是我们如何设置 cron 作业应该运行的时间间隔。

## Cron 语法

```
* * * * * *
| | | | | |
| | | | | day of week
| | | | month
| | | day of month
| | hour
| minute
second ( optional ) 
```

Enter fullscreen mode Exit fullscreen mode

### 允许值

| 字段类型 | 价值 |
| --- | --- |
| 第二 | 0-59 |
| 分钟 | 0-59 |
| 小时 | 0-23 |
| 一月中的某一天 | 1-31 |
| 月 | 1-12 或姓名，即一月 |
| 星期几 | 0-7 或姓名，即星期一 |

#### 使用多个值

您可以使用多个值，并用逗号分隔

```
// Import needed libraries
const express = require("express");
const Cron = require("node-cron");
const fs = require("fs");

// Initialize express
const app = express();

// Set  our default port
app.set("port", process.env.PORT || 3456);

// Schedule a cron job task
Cron.schedule("1,2,4,5 * * * *", () => {
    console.log("> After every minute 1, 2, 4 and 5 the cron job runs")
})

// Initialize our server
app.listen(app.get("port"), err => {
  // If something goes wrong when initializing the server
  if (err) console.log(`Server failure due to ${err.message}`);
  console.log(`Server running on port ${app.get("port")}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用范围

```
// Import needed libraries
const express = require("express");
const Cron = require("node-cron");
const fs = require("fs");

// Initialize express
const app = express();

// Set  our default port
app.set("port", process.env.PORT || 3456);

// Schedule a cron job task
Cron.schedule("1-5 * * * *", () => {
    console.log("> After every minute to 1 from 5 the cron job runs")
})

// Initialize our server
app.listen(app.get("port"), err => {
  // If something goes wrong when initializing the server
  if (err) console.log(`Server failure due to ${err.message}`);
  console.log(`Server running on port ${app.get("port")}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用月份名称作为完整或简短格式的多个值，`January, February, September`或`Jan, Feb, Sep`。

让我们创建一个 cron 作业计划，在第一分钟
后删除一个文件

```
$ touch tobedeletedfile.txt 
```

Enter fullscreen mode Exit fullscreen mode

```
// Import needed libraries
const express = require("express");
const Cron = require("node-cron");
const fs = require("fs");

// Initialize express
const app = express();

// Set  our default port
app.set("port", process.env.PORT || 3456);

/**
 * Cron job that deletes a file after every minute
 */

const deleteFileCron = Cron.schedule("* * * * *", () => {
  console.log("Job has started");
  fs.unlink("./tobedeletedfile.txt", err => {
    if (err) {
      console.log(`Failed to delete file due to ${err.message}`);
    } else {
      console.log("File has been deleted successfully");
    }
  });
});

// Its a scheduled task method that starts a scheduled task
deleteFileCron.start()

// Initialize our server
app.listen(app.get("port"), err => {
  // If something goes wrong when initializing the server
  if (err) console.log(`Server failure due to ${err.message}`);
  console.log(`Server running on port ${app.get("port")}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行这个代码，你应该在你的控制台/终端上看到类似的东西

```
Server running on port 3456
Job has started
File has been deleted successfully
Job has started
Failed to delete file due to ENOENT: no such file or directory, unlink './tobedeletedfile.txt' 
```

Enter fullscreen mode Exit fullscreen mode

# 简单节点 Cron 作业模板

```
[...]
const Cron = require("node-cron");

[...]

Cron.schedule("* * * * *", () => {
    // You can right the task to run here
});

[...] 
```

Enter fullscreen mode Exit fullscreen mode

# Cron 作业优于无限循环

有些开发人员使用无限循环来代替 cron 作业，这是不应该的，因为与 cron 作业相比，循环往往会占用系统中的大量内存。

**cron jobs 的优势**

*   当它运行时，你可以控制它。
*   编写代码和管理其操作更容易。它消除了任务中的循环和计时逻辑，您可以运行 crontab 来更改计时或关闭计时。
*   它不运行的时候不会占用你系统的内存。
*   如果它由于某种原因失败并退出，它将在适当的时候再次启动。

**回路的优点**

*   它没有每次需要时重新启动的开销

# 在哪里使用 Cron 作业

您可以在许多情况下使用 cron 作业，例如:

*   电子邮件自动化
*   数据库备份
*   日志等文件的删除

# 总结

*   我们用 Node 设置了一个简单的 cron 作业，让我们在每分钟后删除一个文件。
*   `cron's`语法的简要描述
*   `cron jobs`的优点
*   在哪里使用 cron 作业

# 资源和建议

下面是一个链接列表，你可以仔细阅读以深入了解 Node 中的 cron 作业以及关于`crons`的一般信息:

*   CodeBeast 的 Cron 作业示例[此处](https://scotch.io/tutorials/nodejs-cron-jobs-by-examples)
*   `node-cron`关于国家预防机制的文件[此处](https://www.npmjs.com/package/node-cron)
*   Cron 作业与节点按有用角度[此处](https://usefulangle.com/post/115/nodejs-cron-job)
*   关于`crons`、`crontabs`、`cron jobs`和[的一切在这里](https://en.wikipedia.org/wiki/Cron)

# 加贺

*   回购[环节](https://github.com/werickblog/cron_jobs_with_nodejs)

封面图片由[https://liyasthomas.github.io/banner/](https://liyasthomas.github.io/banner/)制作