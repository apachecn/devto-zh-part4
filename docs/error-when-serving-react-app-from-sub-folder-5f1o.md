# 从子文件夹提供 React 应用程序时出错

> 原文：<https://dev.to/sudiukil/error-when-serving-react-app-from-sub-folder-5f1o>

你好。

我试图从子文件夹中提供一个 React 应用程序。它目前是一个运行在根 URL 上的非常简单的 SPA(例如 [http://myserver:3000](http://myserver:3000) )，我想从一个子文件夹中提供它(例如[http://my server:3000/new root](http://myserver:3000/newroot))。

我花了一天时间尝试了许多事情，其中大部分都围绕着我的*包中的*“主页”*变量的使用*

```
{  "name":  "cra_test",  "version":  "0.1.0",  "private":  true,  "homepage":  "/newroot",  ... 
```

我发现许多人(和文档)报告这是一个(部分)解决方案，但当我这样做并重新构建然后重新运行我的应用程序(`npm run build`和`serve -s build`)时，它只产生一个空白页面，并在浏览器控制台中显示以下错误:

[![screenshot](img/abb713962b0fa6a49d57e2446d530fc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CVex_T_R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8JuQS5D.png)

原谅前两个警告上的法语字面意思，它是这样写的:

> 位于[]的脚本...]已加载，但其 MIME 类型(文本/html)不是有效的 JavaScript MIME 类型

这两个错误**是在我的 *package.json* 中使用*的【首页】*时独家出现的。出于测试目的，我在一个新创建的(带有 *create-react-app* )应用程序上重现了所有这些步骤，问题是相同的，所以很可能不是因为编码错误。**

经过几个小时的反复试验，我基本上不知道如何解决这个问题。

对于这个问题的任何见解或对最初问题的任何替代解决方案(从子文件夹中提供应用程序)，我将不胜感激，预先感谢！