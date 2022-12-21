# 创建一个 Twitter NASA 应用程序🚀

> 原文：<https://dev.to/twitterdev/build-a-twitter-nasa-app-57kb>

###### 该项目托管于格林奇:【https://glitch.com/~dailynasa T2】

NASA 每天都会通过他们的公共 API 发布一张天文学或空间科学相关的图片。这些图像非常酷，并且告诉你我们居住的不可思议的太阳系！

作为一名天文爱好者，我想知道如何轻松查看这些图像，甚至让它们出现在我的 Twitter 时间轴上。我开始着手这项工作，并最终完成了这个全自动的 NASA 每日应用程序，它可以在推特上发布 NASA 每天发布的图片。

我们的 node.js 应用程序将连接到 NASA API，临时存储提供的媒体，并通过 Twitter API 发布标题为+图像/视频的推文。这里有一个[的例子](https://twitter.com/DailyNASA/status/1161986403653292035)。相当简单！

在本教程中，我还将向您展示如何使用 Twitter 和 NASA APIs 来构建这个应用程序。要查看应用程序的运行情况，请查看 Twitter 页面 [@DailyNASA](https://twitter.com/dailynasa) 。

## 我们今天要做什么？

我们的应用程序将被托管在 [Glitch](https://glitch.com/~dailynasa) 上，所以除了确保我们的应用程序被正确配置之外，我们不会做太多事情。

一旦我们的项目被重新混合并且`.env`文件的密钥和令牌被正确配置，我们将安排一个 cron-job 来使我们的应用程序发出 Tweet。cron-job 本质上是一个自动唤醒呼叫。

## 代码到底是怎么工作的？

1.  该应用程序将通过网络挂钩激活。我们的 cron-job 将在一天中的特定时间 pinged 这个 webhook⏰
2.  我们的应用程序将连接到美国宇航局的 API🚀
3.  该应用程序将检查 NASA API 提供的媒体是图像 URL 还是视频 URL。如果是一张照片，我们的应用程序会下载图片并发布。如果是视频，该应用程序将只发布视频网址:
    *   **视频**📹-应用程序将保存视频 URL +标题，并通过 Twitter [状态/更新](https://developer.twitter.com/en/docs/tweets/post-and-engage/api-reference/post-statuses-update)端点发布。这里有一个[的例子](https://twitter.com/DailyNASA/status/1151477103306649604)。
    *   **图像**📷-应用程序会将图像保存到一个临时目录，并通过 Twitter [状态/更新](https://developer.twitter.com/en/docs/tweets/post-and-engage/api-reference/post-statuses-update)端点发布。这里有一个[的例子](https://twitter.com/DailyNASA/status/1161986403653292035)。因为这是一个临时目录，我们不必担心填满空间(一语双关)🥁

## 先决条件

在我们开始之前，您需要以下内容:

*   Twitter 开发者账户- [点击此处](https://t.co/developeraccount)申请

*   一个注册了的推特应用程序 - [点击这里](https://cdn.glitch.com/40a2e399-2bcb-4c11-b356-fdb1d9baa5e9%2FScreen%20Shot%202019-08-08%20at%205.20.15%20PM%20copy.png?v=1565299668056)看看怎么做

*   你的 [Twitter API](https://developer.twitter.com/en/apps) 密钥和令牌的副本- [点击这里](https://cdn.glitch.com/40a2e399-2bcb-4c11-b356-fdb1d9baa5e9%2FScreen%20Shot%202019-08-08%20at%205.20.35%20PM%20copy.png?v=1565300041251)查看如何操作

*   美国宇航局 API 密匙🔑- [点击此处](https://api.nasa.gov/#apply-for-an-api-key)获取一个

*   cron-job 帐户- [点击此处](https://cron-job.org/en/signup/)进行注册

一旦你有了所有这些，你就可以开始了🚀！

## 菜谱

第一步💿

a)在我们的 [Glitch 项目页面](https://glitch.com/~dailynasa)上，重新混合这个项目！点击本页左上角的“重新混合项目”按钮。

[![Remix](img/e495f079895c206a4699cb0ea6141e13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rKLuwUSa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.glitch.com/40a2e399-2bcb-4c11-b356-fdb1d9baa5e9%252FScreen%2520Shot%25202019-08-08%2520at%25203.54.26%2520PM.png%3Fv%3D1565294080315)

b)命名您的应用程序。

我叫我的`dailynasa`

[![Name](img/02cf2b1f5a49bb8ca4af96b2a3672779.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k4Ln--tM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.glitch.com/40a2e399-2bcb-4c11-b356-fdb1d9baa5e9%252FScreen%2520Shot%25202019-08-08%2520at%25203.57.37%2520PM.png%3Fv%3D1565294269445)

第二步🔑

a)在项目`.env`文件中输入您的 Twitter 应用密钥和令牌

[![Keys](img/0327ba5a420e67cc31be4b3dd3ddc582.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M1K3xKx0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.glitch.com/40a2e399-2bcb-4c11-b356-fdb1d9baa5e9%252FScreen1%2520Shot%25202019-08-08%2520at%25205.20.35%2520PM%2520copy.png%3Fv%3D1565300721909)

b)在项目`.env`文件中输入您的 NASA API 密钥

c)为您的应用程序端点命名

例如，如果我将我的文件标记为`wakeup`，那么你的`.env`文件应该是这样的:`BOT_ENDPOINT='awaken'`。我的最终端点将如下所示:`https://dailynasa.glitch.me/wakeup`

🚨重要-保持所有访问密钥、令牌和端点名称的私密性🚨

步骤 3 -测试测试！！

现在您已经配置了我们的应用程序，让我们来测试一下。

我通常建议在工具→日志中查看日志，以验证我们的应用程序在唤醒时是否运行。

在新的浏览器窗口中，输入您唯一的端点 URL 并按 enter 键。(您也可以通过命令行使用以下命令测试 URL:`curl https://dailynasa.glitch.me/wakeup`

稍等一会儿，你的推特账户上就会出现美国宇航局发布的每日推文。

现在您已经验证了它的工作，让我们确保它是自动化的，这样您就不必每天都这样做了。

第四步-⏰

我们将使用[cron-job.org](https://cron-job.org/en/)来 ping 我们的端点，并让我们的应用程序发布每日推文。

创建一个[新的 cron-job](https://cron-job.org/en/members/jobs/add/) 并填写页面:

[![cron-job](img/9a97f5264319706b73b3063255b29245.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---KpoW5UZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.glitch.com/40a2e399-2bcb-4c11-b356-fdb1d9baa5e9%252FScreen%2520Shot%25202019-08-08%2520at%25206.13.22%2520PM.png%3Fv%3D1565302473410)

🚨重要的是——安排你的应用过于频繁地运行会显得很垃圾，并导致违反 Twitters 的自动化政策。🚨

一旦作业运行，您可以在[页面](https://cron-job.org/en/members/jobs/)上查看响应。

就这样，我们结束了！

## 恭喜恭喜！

你做到了！😎

如果你喜欢从教程中学习或有任何问题，请查看 Twitter 的社区论坛。