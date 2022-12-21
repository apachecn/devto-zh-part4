# 从销售或店面开始

> 原文：<https://dev.to/alexalexyang/starting-with-saleor-storefront-44e7>

这是为了记录我如何开始使用 [saleor-storefront](https://github.com/mirumee/saleor-storefront) 0.6.0。这是用 React 做的 [saleor](https://github.com/mirumee/saleor) 0.0.0 的前端。

在这个小的演练中，我将讨论我在尝试部署它们时遇到的一些问题。我希望这能让 saleor 的入门变得简单一点。

网上的大部分资料表明，作者在同一台机器上同时运行 storefront 和 saleor。由于一些问题，我无法让它工作，包括店面不会像 AbdulWaheedPasha 在这里描述的那样运行。

在 AbdulWaheedPasha 让我知道这个问题之前，我认为这是因为我的许多项目给我的 Ubuntu 实例带来了太多压力。因此，我决定减轻一点负担，将店面部署到 Netlify。

我从我们将 saleor repo 克隆到我们的 Ubuntu 机器上之后开始，我们已经将 saleor-storefront 分支或推送到我们自己的 Git repo。

但是，在部署到 Netlify 之前，我们必须正确设置 ALLOWED_HOSTS。这是在 saleor 网站上完成的。

# 设置 ALLOWED_HOSTS 为*

我想在我的 Ubuntu 实例上将 saleor 部署到 https://example.com，在 Netlify 上将 saleor-storefront 部署到 https://storefront.example.com 的 T2。

在这种情况下，因为我通过执行`docker-compose up`(有时使用`-d`)来运行 saleor，所以我决定将`ENV ALLOWED_HOSTS 'storefront.example.com'`添加到 saleor/Dockerfile:

```
EXPOSE 8000
ENV PORT 8000
ENV PYTHONUNBUFFERED 1
ENV PROCESSES 4
ENV ALLOWED_HOSTS 'storefront.example.com' 
```

Enter fullscreen mode Exit fullscreen mode

这不起作用。店面不断返回“无效的主机标题”或只是一个空白页面有时。所以，我尝试了`ENV ALLOWED_HOSTS 'https://storefront.example.com'`来代替。不起作用。

大多数网上的解决方案都说设置为`*`，我觉得这可能有点安全问题，因为它会允许任何人从销售或后端获取数据。不管怎样，我还是去了，看看我如何不能用一个特定的 URL 解决问题。现在是这样的:`ENV ALLOWED_HOSTS '*'`。

在我们继续之前，记得根据[文档](https://docs.saleor.io/docs/getting-started/intro/)运行类似`npm run build-assets`和`npm run build-emails`的命令。

你也应该运行`python3 manage.py migrate`和`python3 manage.py createsuperuser`来管理你的站点。如果你也像我一样选择使用`docker-compose up`，你可以用`docker exec -it saleor_web_1 bash`进入 saleor 容器。然后运行 migrate 和 createsuperuser 命令。

接下来，我们转向销售或店面方面的事情。

# 将 saleor-storefront 部署到 Netlify

我想这应该很简单。按照 Netlify 的说明部署 repos。在这里，我将重点讨论店面特有的东西。

您的部署设置位于“站点设置”>“构建和部署”中。它们应该是这样的:

构建命令:`npm run-script build`
发布目录:dist

它应该可以成功构建，但是站点会遇到一些问题，下面会讨论。

# 确保后端 URL 设置正确

【2019 年 10 月 7 日更新:已改为`API_URI`。

店面[先决条件](https://github.com/mirumee/saleor-storefront#prerequisites)说:“要运行店面，你必须设置 BACKEND_URL 环境指向 Saleor 实例。如果您使用默认设置在本地运行 Saleor，请将 BACKEND_URL 设置为: [http://localhost:8000/](http://localhost:8000/) 。

这并不简单。在继续之前让我们回顾一下:我已经在我的 Ubuntu 实例上将 saleor 部署到了 https://example.com，在 Netlify 上将 storefront 部署到了 https://storefront.example.com 的 T2。

为了设置`BACKEND_URL`，我运行了`export BACKEND_URL=https://example.com`。

然而，它返回 404。查看控制台，我发现请求的 URL 是:[https://store front . example . com https://example . com/graph QL/](https://storefront.example.comhttps://example.com/graphql/)

不知何故，它将店面 URL 连接到销售者后端 URL。

我想也许我必须从 graphql 查询代码中删除店面 URL。既然是 graphql，我以为必须在 saleor-store front/Apollo . config . js 中设置网址，这是错误的。

经过多次尝试，我终于明白我必须打开 saleor-store front/config/web pack/config . base . js 并找到这个部分:

```
new webpack.EnvironmentPlugin({
      "BACKEND_URL": "http://localhost:8000/",
      "SERVICE_WORKER_TIMEOUT": "60000"
    }) 
```

Enter fullscreen mode Exit fullscreen mode

然后我想我可以简单地取消设置`BACKEND_URL`环境变量并将正确的 URL 放在这里。这只是再现了问题。

解决方案是设置`BACKEND_URL`环境变量并完全删除 config.base.js 中的 URL，使其为空:

```
"BACKEND_URL": "", 
```

Enter fullscreen mode Exit fullscreen mode

终于，成功了。

# 商品页面不会加载，那么 404

当网站正常运行后，我试着访问我之前在管理面板上制作的一个产品。由于类型错误，该页面无法加载。关于`price`是`undefined`的一些事情。

在重新加载页面几次后，它返回 404，并保持不变。

我尝试了很多我已经不记得的事情，最后决定从头开始。我在 saleor 后端运行了`docker-compose down`，删除了 saleor_web_1 容器和 saleor_web 图像，它们是用于 saleor Django 后端的。我离开了 redis 和芹菜容器和图像。然后我又跑了一次`docker-compose up`。

现在成功了。但是...它加载时没有产品描述。

这就是我现在的处境。

我希望边解决问题边写作。请让我知道你是否能帮助解决正在发生的问题。