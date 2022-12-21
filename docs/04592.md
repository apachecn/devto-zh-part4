# 只是另一个 React 模板🤷‍♂️

> 原文：<https://dev.to/droidmakk/just-another-react-template-36n8>

嘿，戴夫们👩‍💻👨‍💻,

好久没写东西了。发生了很多事情，就像，我应该说这是一场过山车，但我还没有走到一半。所以我想我可能会张贴一些东西，如果/可能你们中的一些人会发现它是有用的。

[反应-模板](https://github.com/droidMakk/react-template)

也许只是另一个反应模板。我正在添加我觉得有趣且易于开发的功能。我在那里找到了这个插件叫做 craco T1，它可以扩展 create-react-app 的配置而不弹出应用😱。那是另一个层面😎。

已经配置了不同的脚本，这些脚本主要由世界各地的开发人员使用。我觉得有趣的一些其他特性是这样的，

*   [故事书](https://storybook.js.org/)
*   [节点门](https://nodemon.io/)
*   [PM2](http://pm2.keymetrics.io/)
*   [反应路由器](https://reacttraining.com/react-router/web/guides/quick-start)替换为到达路由器
*   [灯塔🚨CLI](https://github.com/GoogleChrome/lighthouse/tree/master/lighthouse-cli)
*   [Gitlab 管道](https://docs.gitlab.com/ee/ci/pipelines.html)

我计划增加更多的功能，

*   [web 提示](https://webhint.io/)报告正在进行中
*   使用 pm2 / Nginx 将 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 发送到主机
*   扩展服务人员...还会有更多

如果你想让我补充什么或者你觉得有趣，请告诉我。我会跳进去，如果我们在同一页上，将简单地放在列表中，稍后将被合并到基础中。

文件夹结构本身是我发现有趣的是，在完成几个项目后，我发现它迫使**数据服务视图层**做得很好，并且**看起来组件正在成为 web 的未来**。所以最好有一个像
这样的标准文件夹结构

```
src/
    |--components/
        |-- ComponentName/
            |-- index.js ( adding mappers and context )
            |-- Component.js ( your component's code )
            |-- componentname.(css|scss) ( Your component's styling )
    | --screens/
        |--ScreenName/
            |--index.js ( mapper and context )
            |--Screen.js ( your screen's code )
            |--components/ ( screen specific code )
                |-- ComponentA.js
                |-- ComponentB.js
            |--screenname.(css|scss) ( Screen's styling / overrides ) 
```

Enter fullscreen mode Exit fullscreen mode

让我知道是否有更多的东西，所以我会简单地合并到基础中，并在未来的迭代中使用它。

早上好，朋友们！如果你想让我补充任何建议，请随时给我发 DM/Whatsapp/邮件。
🤙+91-8939717211 |[@ droid makk](https://twitter.com/droidmakk)|