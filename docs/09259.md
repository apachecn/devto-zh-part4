# 启动底漆

> 原文：<https://dev.to/tomerbendavid/startup-primer-2on5>

# 启动引物

## 用 docker 运行 jekyll

```
## Run jekyll with dockerexport JEKYLL_VERSION=3.5
docker run --rm \
  --volume="$PWD:/srv/jekyll" \
  -p 4000:4000 \
  -it jekyll/builder:$JEKYLL_VERSION \
  jekyll serve 
```

## 域名

1.  [https://tld-list.com/-比较域名续费价格](https://tld-list.com/)

## 网站/博客/...

1.  [超赞的哲基尔主题](https://github.com/planetjekyll/awesome-jekyll-themes)
2.  [静态站点动态](http://staticman.net/)
3.  [youmightnotneejquery . com](http://youmightnotneedjquery.com/)

### 静态网站

1 [哲基尔播客](https://github.com/taxigy/itunes-jekyll-template)

## 支付、订阅、电子商务

1.  [https://gumroad.com](https://gumroad.com/)

## 启动和引导

1.  [levels.io 引导](https://levels.io/bootstrapping/)
2.  [许多创业资源](https://siftery.com/)

## UI 与平面设计

1.  [logo 设计 shapefactory](https://shapefactory.co/)
2.  [https://crello.com——你自己的图形设计师](https://crello.com/)
3.  [https://pixabay.com](https://pixabay.com/)
4.  [https://unsplash.com/](https://unsplash.com/)
5.  [va 能成为你自己的平面设计师吗](https://www.canva.com/)
6.  [todomvc 选择 UI 框架](http://todomvc.com/)
7.  [引导组件片段](https://github.com/tomer-ben-david/tomer-ben-david.github.io/blob/master/_posts/2017/bootsnipp.com)
8.  [平纹颜色](https://github.com/tomer-ben-david/tomer-ben-david.github.io/blob/master/_posts/2017/flatuicolors.com)
9.  [颜色模式](https://styled-components.com/color-schemer/)

## 安卓

1.  [安卓图标生成器](https://romannurik.github.io/AndroidAssetStudio/icons-launcher.html#foreground.type=clipart&foreground.clipart=android&foreground.space.trim=1&foreground.space.pad=0.25&foreColor=rgba(96%2C%20125%2C%20139%2C%200)&backColor=rgb(68%2C%20138%2C%20255)&crop=0&backgroundShape=square&effects=none&name=ic_launcher)
2.  [安卓功能图](https://www.norio.be/android-feature-graphic-generator/)

## 李森宁软件

1.  [keygen.sh](https://keygen.sh/)

## 营销和引导创业

1.  [prospect.io 领导和电子邮件营销](https://prospect.io/)
2.  [缓冲多点](https://buffer.com/)
3.  [忍者激光](http://ninjablaster.com/)
4.  胡特苏特

## 文档

1.  [XWiki](http://xwiki.org/)

## 招聘

1.  [合格. io](https://www.qualified.io/)

## 打造

1.  [Polygot Maven](https://github.com/takari/polyglot-maven)

## 版本控制

1.  [远程托管 Github.com](https://github.com/)
2.  [自助免费](https://gogs.io/)
3.  [MailChimp](https://github.com/tomer-ben-david/tomer-ben-david.github.io/blob/master/_posts/2017)
4.  [小贴士！！](http://blog.travelpayouts.com/en/travel-site-with-android-application/?utm_source=tp_dashboard&utm_medium=news&utm_campaign=en)

## 标图

1.  [redash . io](https://dev.tovisualize%20your%20company%20data)[[https://redash . io](https://redash.io/)
2.  [黑客密谋新闻](https://news.ycombinator.com/item?id=12897415)
3.  [谷歌数据工作室](https://www.google.com/analytics/data-studio/)
4.  阿帕奇齐柏林飞艇
5.  [喀土穆. com](https://chartio.com/)
6.  [类似于 redash](http://alternativeto.net/software/redash/)

## 一般书库

1.  [https://stackshare.io](https://stackshare.io/)

# 杂项

1.  [copyfish 从 ocr pdf 图像中提取文本视频 https://news.ycombinator.com/item?id=14593906](https://news.ycombinator.com/item?id=14593906)

# 备注

Ionic 2017 开发者调查让我们知道您正在使用哪些服务和工具来构建应用，从而帮助我们改善您的 Ionic 体验。我们将利用这些反馈来更好地将 Ionic 与流行的服务整合起来，并改进我们的工具和功能。在我们开始之前，请向我们简单介绍一下你自己，帮助我们理解你的答案。

*   所需的后端技术您目前在 Ionic 应用中使用哪些后端服务？您使用什么服务来发送推送通知？* Firebase/GCM 亚马逊 SNS OneSignal 城市飞艇 Pushwoosh 离子推送定制推送系统(node-apns 等)我不发送推送通知其他:

这是一个必须回答的问题，您使用什么数据库作为后端？* Firebase 自托管 SQL 数据库(MySQL、Postgres 等)。AWS dynamo DB AWS RDS Heroku Postgres 自托管 MongoDB Cloud hosted MongoDB Ionic DB(beta)我不使用数据库其他:
这是一个必答问题，你如何托管你的应用的服务器端？*没有服务器！Firebase 或另一个纯客户端系统 AWS Lambda Amazon ECS AWS light sail Google App Engine/GCP Containers Heroku Azure Digital Ocean Classic VPS provider(Linode、Rackspace Cloud 等)自托管虚拟服务器自托管 docker containers 自托管专用服务器其他:
这是一个必答问题，您的应用程序在什么后端技术上运行？* Node.js Python (Django、Flask 等)Ruby (on rails 或其他)Java C#/。NET Other:
这是一个必须回答的问题，你使用什么服务进行分析？* Google Analytics Firebase Analytics Amazon Mobile Analytics Fabric Answers Mixpanel Keen Segment Amplitude Localytics Adobe Ionic Analytics 自定义分析(自托管、自建等)其他:
这是一个必答问题，您如何管理用户身份验证？* Firebase Auth GCP Auth Auth . io Auth 0 storm path Fabric Digits Amazon Cognito Azure authentic ation Ionic Auth Custom oAuth Other:
这是一个必问的问题你如何在云中测试你的应用？* AWS Device Farm Firebase Test Lab browsers stack Sauce Labs 自定义设备测试系统我不做云设备测试其他:
这是一个必答问题，你是如何跟踪你的 app 中的错误和崩溃的？* Fabric/Crashlytics Firebase 崩溃报告 Sentry track js Bugsnag op beat roll bar 自定义系统我不跟踪错误或崩溃其他:
这是一个必答问题您如何远程更新您的应用程序？* Ionic Deploy Code Push PhoneGap ContentSync PhoneGap Hydration 自定义更新系统我不会远程向我的应用程序推送更新其他:
这是一个必答问题下一页第 1 页，共 3 页切勿通过 Google Forms 提交密码。这种形式是在爱奥尼亚内部创造的。举报滥用-服务条款-附加条款谷歌表单
Ionic 2017 开发者调查

*   所需工具和测试用于开发和测试 Ionic 应用程序的附加工具您使用什么文本编辑器或 IDE？* Visual Studio 代码 Visual Studio IDE Atom web storm Sublime Text TextMate 括号其他:

使用任何应用原型工具？* Ionic Creator Invision 漫威·巴斯拉米克草图我不使用任何应用原型制作工具其他:
你如何进行 beta 测试并收集反馈？* Ionic View Hockeyapp test flight test fairy 我不使用任何 beta 测试/反馈工具其他:
你是如何把你的应用分发给用户的？* App store(苹果、Google Play 等)MobileIron Airwatch 微软 Intune Ionic View(作为内部应用商店等)托管网络应用或桌面下载其他:
后退下一页第 2 页，共 3 页切勿通过谷歌表单提交密码。这种形式是在爱奥尼亚内部创造的。举报滥用-服务条款-附加条款谷歌表单