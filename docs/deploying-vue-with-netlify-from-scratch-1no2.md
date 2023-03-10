# 从头开始使用 Netlify 部署 Vue

> 原文：<https://dev.to/lpellis/deploying-vue-with-netlify-from-scratch-1no2>

# 使用 Netlify 部署 Vue，从零开始

Netlify 是一个伟大的 vue 应用平台。您可以简单地进行 git 推送，他们会自动为您构建和部署它。他们还有一个很棒的免费层。

在 Netlify 上部署 Vue 非常简单:

## 1:创建 Vue app

在本例中，我将使用 [Vue CLI](https://cli.vuejs.org/) ，因此请确保已安装。

```
vue create vue-sample 
```

我选择了默认设置，并使用 npm 作为包管理器。
创建后，您可以使用
运行该示例

```
cd vue-sample
npm run serve 
```

<figure>[![](img/7504683976b18721db802f6886e4db71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HoaUkyS1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ximg/image/upload/w_732%2Ch_500%2Cc_limit/f_auto/whoami/4a595384/post/3okGr7aFuAg02xxZ.png) 

<figcaption></figcaption>

</figure>

## 2:与网络连接

Netlify 可以从 Github、Bitbucket 或 GitLab 部署，所以把你的代码推到其中一个。接下来是时候用 Netlify 创建一个新站点了:

<figure>[![](img/7c0b70add457f4f8db9c8dc830da35f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8rWHPWJt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ximg/image/upload/w_732%2Ch_500%2Cc_limit/f_auto/whoami/4a595384/post/3A4kkOjaHA9sMOgf.png) 

<figcaption>在 Netlify 中添加新站点</figcaption>

</figure>

选择您的回购，然后继续主设置:

<figure>[![](img/cbe52935ec2e2c5923da0ac86ef62e5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zo6bHWML--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ximg/image/upload/w_732%2Ch_500%2Cc_limit/f_auto/whoami/4a595384/post/NqxLcDScQqG4FXcn.png) 

<figcaption></figcaption>

</figure>

build 命令是 Netify 在推送代码后运行的命令。`npm run build`将把生成的文件放在`dist/`文件夹下，所以我们告诉 Netlify 发布那个目录。

此时，您的站点将处于活动状态，更好的是，每次推送到 master 时都会自动部署一个新版本！不过，要让一切正常运转，还有一些最后的步骤。

## 3:设置重定向规则

虽然主页运行良好，但目前直接进入任何内页都会导致 404 错误。要解决这个问题，让我们首先创建一个路由来查看问题:

```
vue add router 
```

<figure>[![](img/9ad3d7cff5b4fc01240e575159a2c8e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--19XeURsf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ximg/image/upload/w_732%2Ch_500%2Cc_limit/f_auto/whoami/4a595384/post/E5AnwgzOm7QkXMMT.png) 

<figcaption>选择历史模式以获得好看的网址，例如/关于</figcaption>

</figure>

将这些更改提交并推送到 master，Netlify 将自动部署新版本。您应该会在网站顶部看到一个“关于”链接:

<figure>[![](img/21e01295519269cf67d7332331204896.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--daohanxg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ximg/image/upload/w_732%2Ch_500%2Cc_limit/f_auto/whoami/4a595384/post/L6pgmEQ3yg3jt7i7.png)

<figcaption>/关于</figcaption>

</figure>

如果你直接刷新页面，虽然你目前得到 404。

<figure>[![](img/9e63e2497741edded5bc862b8dbc2418.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jELcPTUU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ximg/image/upload/w_732%2Ch_500%2Cc_limit/f_auto/whoami/4a595384/post/holDRfP5HlJFpCUW.png)

<figcaption>/如果直接去那里的话</figcaption>

</figure>

要解决这个问题，我们需要设置[自定义重写规则](https://www.netlify.com/docs/redirects/#rewrites-and-proxying)。在`public/`文件夹下创建一个名为`_redirects`的文件，内容如下:

```
/*    /index.html   200 
```

这将有效地服务于 index.html 的任何路线下你的网站。将它放在`public/`目录中意味着它将在`npm run build`之后的`dist/`中结束，这就是我们想要的。

现在把这个推给师父就大功告成了！

<small>最初发布于[https://loft ie . com/post/deploying-vue-with-net lify-from-scratch](https://loftie.com/post/deploying-vue-with-netlify-from-scratch)T3】</small>