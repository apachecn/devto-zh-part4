# 在 7 分钟内构建一个 Slack 聊天机器人并免费托管它的完整指南

> 原文：<https://dev.to/configcat/complete-guide-to-build-a-slack-chatbot-in-7-minutes-and-host-it-for-free-1ef8>

#### 基于 GitHub 的 Hubot。连接到 Slack。部署在 Heroku。

我对机器人情有独钟，想做一个聊天机器人作为实验，它会调用我的服务的([ConfigCat.com](https://configcat.com))`\health`端点，如果一切正常，它就会返回。我花了相当长的时间来浏览可能的框架和文档，阅读所有过时的指南，以找到最快和最便宜的方法。我觉得这值得分享。

所需工具:

*   [Node.js](https://nodejs.org) 已安装
*   [时差](https://slack.com)工作空间
*   免费的 Heroku 帐户
*   首选代码编辑器

# 将 Hubot 集成添加到您的闲置工作空间

#### [在 Slack App 目录中找到 Hubot](https://configcat.slack.com/apps/A0F7XDU93-hubot)

#### 连接到工作区

[![connect](img/07c8b705e5a25f7cf48fcf98ca0b0d23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k7zWEVeH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r8exqwybhdhjp97o4tdd.png)

[![name](img/3b42fbc291ed9c006565059b56eb3af0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--95XnWsgV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h017kdh2gbilt2mdr7ce.png)

#### 记下 API 令牌，您稍后会用到它

[![token](img/839e60af3700fef160be766cd0e40604.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JtXDkRUs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nffhcil1vwn6y8kqxcw4.png)

# 在本地机器上运行 Hubot

#### 安装[约曼](https://yeoman.io)和 Hubot 发电机

```
npm install -g yo generator-hubot 
```

#### Hubot 项目脚手架 a

```
mkdir catbot
cd catbot
yo hubot --adapter=slack 
```

#### 使用 API 令牌启动 Hubot

```
HUBOT_SLACK_TOKEN=xoxb-271695489427-739714865891-Z5gPPiuTORKDFO4QvqKe1B9y ./bin/hubot --adapter slack 
```

#### 打开 Slack，开始对话

聊天机器人应该在应用程序下可用。

[![slack](img/d7c61959a59e1bca7b0eab18c85d89cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P5vy6R34--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2zaw1hll2e8oaktq0k7.png)

#### 用`help`命令测试

[![help](img/4df6b774058574fb37ce1ea31539f6cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QxmBW_I5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xkl1ynoanx7xm1ujt868.png)

# 识别`health`命令

并对 ConfigCat 的`/health`端点进行 HTTP GET 调用，并通过 Slack 回复结果。

我在`/scripts`文件夹下创建了一个`configcat.coffee`，代码如下:

```
module.exports = (robot) ->
    robot.hear /health/i, (reply) ->
        robot.http("https://api.configcat.com/api/v1/health")
        .get() (err, res, body) ->
            reply.send body 
```

[查看 GitHub 上的完整源代码。](https://github.com/configcat/catbot-example)

我用 CoffeeScript 是因为我喜欢尝试，感觉很新奇。但是您也可以使用 JavaScript。

#### 测试`health`检查是否有效

[![health](img/0c71b5087beb63103de7b131998180b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H_t23Yfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ds4y7wnsch93v4brictp.png)

# 部署到 Heroku

#### 查看 Node.js 版本

```
node --version 
```

打开`package.json`查看 Node.js 版本。在我的例子中，生成的`package.json`是`"node": "0.10.x"`，所以我把它改成了:

```
"engines":  {  "node":  "10.16"  } 
```

#### 去委员会

确保您已经创建了一个 git 存储库，并且您的工作已经提交。

```
git init
git add .
git commit -m "Initial commit" 
```

#### 英雄库 CLI

安装 [Heroku CLI。](https://devcenter.heroku.com/articles/heroku-cli)然后登录。

```
heroku login 
```

创建应用程序。

```
heroku create 
```

为 API 令牌设置环境变量。

```
heroku config:set HUBOT_SLACK_TOKEN=xoxb-271695489427-739714865891-Z5gPPiuTORKDFO4QvqKe1B9yt --app=arcane-dusk-29327 
```

#### 去推

```
git push heroku master 
```

# 避开睡眠模式

由于我正在使用 Heroku 的免费计划，该应用程序最终将进入睡眠模式。为了避免这种情况，请在您的聊天机器人中添加 [hubot-eroku-keepalive](https://www.npmjs.com/package/hubot-heroku-keepalive) 脚本。

# 参考单据

*   [Hubot 文档](https://hubot.github.com/docs/)
*   【Hubot 的 Slack 开发套件
*   [Heroku 部署](https://devcenter.heroku.com/categories/deployment)