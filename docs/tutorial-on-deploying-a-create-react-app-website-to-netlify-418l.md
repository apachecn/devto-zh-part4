# 关于使用 HTTPS 将创建-反应-应用程序网站部署到网络生活的教程

> 原文：<https://dev.to/swyx/tutorial-on-deploying-a-create-react-app-website-to-netlify-418l>

## TL；速度三角形定位法(dead reckoning)

单击此处:

[![Deploy to Netlify](img/8f270f0bff04fe4f2060a41c2299611b.png)](https://app.netlify.com/start/deploy?repository=https://github.com/netlify-labs/deploy-create-react-app&utm_source=blog&utm_medium=devto&utm_campaign=devex)

对所有事情都点击“是”!

## 是吗？

是的差不多。这个过程创建了一个 https://github.com/netlify-labs/deploy-create-react-app[的分支(简单地说就是一个有`npx create-react-app deploy-create-react-app`输出的站点)，然后你可以在本地克隆并编辑它。当你完成和`git push origin master`后，你的更改将自动重新部署。](https://github.com/netlify-labs/deploy-create-react-app)

## 我得到了什么？

*   [现成的 SSL](https://www.netlify.com/docs/ssl/?utm_source=blog&utm_medium=devto&utm_campaign=devex)
*   免费定制域名(你可以在那里购买一个域名或者使用从别处购买的域名)
*   [连续部署](https://www.netlify.com/docs/continuous-deployment/?utm_source=blog&utm_medium=devto&utm_campaign=devex)
*   [重定向](https://www.netlify.com/docs/redirects/?utm_source=blog&utm_medium=devto&utm_campaign=devex#history-pushstate-and-single-page-apps)对单页应用有用！

以及[更多功能](https://www.netlify.com/docs/welcome/?utm_source=blog&utm_medium=devto&utm_campaign=devex)

## 其他方式部署

这些[部署按钮](https://www.netlify.com/docs/deploy-button/?utm_source=blog&utm_medium=devto&utm_campaign=devex)仅仅是部署到 Netlify 的一种方式。

*   **如果你在 GitHub/GitLab/BitBucket** 上有一个现有的回购协议，你可以简单地[从那个回购协议创建一个新的站点。](https://app.netlify.com/start?utm_source=blog&utm_medium=devto&utm_campaign=devex)
*   **如果你有一个本地项目**，你可以使用 [Netlify CLI](https://www.netlify.com/docs/cli/?utm_source=blog&utm_medium=devto&utm_campaign=devex) 并运行`netlify init`来初始化一个站点。
*   **如果你不想使用 Netlify 的 buildbot 和连续部署**，你可以运行你自己的构建，直接使用`netlify deploy --prod`将文件夹部署到生产环境中

## 为什么？

这篇教程是为了回应 Reddit 的帖子。