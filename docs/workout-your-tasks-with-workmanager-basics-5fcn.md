# 使用工作管理器锻炼您的任务—基础

> 原文：<https://dev.to/magdamiu/workout-your-tasks-with-workmanager-basics-5fcn>

### 使用工作管理器-主要组件锻炼您的任务

[![](img/9ff70f7af165b410c96e8539f9ebe421.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vWTz8gTF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AgOqQSVM9Ictd7d9T85Yl-A.png)

*“work manager 是管理* ***可推迟*** *和* ***保证*** *后台工作的库。”*

在我的[上一篇文章](https://medium.com/@magdamiu/workout-your-tasks-with-workmanager-intro-db5aefe14d66)中，我详细介绍了 Android 内存模型、电池优化功能、当前的后台处理解决方案，以及 [WorkManage](https://developer.android.com/topic/libraries/architecture/workmanager) 的主要优势和应该在哪里使用。

在这篇博文中，我将介绍 WorkManager 库的主要组件:

*   工人
*   工作请求
*   限制
*   输入输出数据
*   工作管理器

为了在我们的应用程序中使用 WorkManager，首先我们应该声明**依赖关系**:

*   打开**项目**的 build.gradle 文件，添加 google()存储库，如下所示: