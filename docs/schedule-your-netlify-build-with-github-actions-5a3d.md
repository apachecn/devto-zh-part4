# 使用 GitHub 操作计划您的网络构建

> 原文：<https://dev.to/jinksi/schedule-your-netlify-build-with-github-actions-5a3d>

如果你在 Netlify 上有一个网站，你可能知道 [Build Hooks](https://www.netlify.com/docs/webhooks/#incoming-webhooks) 。通过创建一个构建钩子并向它发送一个`POST`请求，您就为您的站点触发了一个新的构建&部署。如果你的构建过程是从第三方服务(例如 Instagram，Youtube)中获取内容，那么安排每日构建&部署是保持这些内容新鲜的一个简单方法。

我遇到的设置预定构建钩子触发器的最简单的方法是使用 [GitHub Actions](https://github.com/features/actions) 。如果你的库是公开的，那么设置是完全免费的。如果你的仓库是私有的，你很可能会留在他们的免费层，见定价[这里](https://github.com/features/actions)。

## 创建预定的网络构建触发 GitHub 操作

1–使用 [Netlify 仪表板](https://app.netlify.com)为您的网站添加一个构建挂钩

> (设置>构建和部署>连续部署>构建挂钩)

2–在你的 GitHub repo 中，在`.github/workflows`目录下创建一个`main.yml`文件:

```
 # .github/workflows/main.yml

   name: Trigger Netlify Build
   on:
     schedule:
       # Run at 0815 daily
       - cron: '15  8  *  *  *'
   jobs:
     build:
       name: Request Netlify Webhook
       runs-on: ubuntu-latest
       steps:
         - name: Curl request
           run: curl -X POST -d {} YOUR_BUILD_HOOK 
```

Enter fullscreen mode Exit fullscreen mode

> 用您刚刚创建的构建钩子 url 替换 **YOUR_BUILD_HOOK** 。
> 
> 你可以使用 [crontab.guru](https://crontab.guru) 轻松生成你的 cron 日程。

3–打开 GitHub repo 中的 Actions 选项卡，按照 cron 计划查看工作流触发器。🎉

希望这对你有用！如果您有任何问题，我已经创建了一个示例存储库，它使用 GitHub 操作每 15 分钟构建一次:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [金克斯](https://github.com/Jinksi)/[netlify-build-github-actions](https://github.com/Jinksi/netlify-build-github-actions)

### 使用 Github 操作调度事件触发网络构建的示例

<article class="markdown-body entry-content container-lg" itemprop="text">

### <g-emoji class="g-emoji" alias="alarm_clock" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/23f0.png">⏰</g-emoji>使用 Github 动作触发基于时间表的 Netlify 构建

1.  使用 [Netlify 仪表盘](https://app.netlify.com/)为你的站点添加一个[构建钩子](https://www.netlify.com/docs/webhooks/#incoming-webhooks)

    > 设置>构建和部署>持续部署>构建挂钩

2.  在 Github repo 中创建一个`.github/workflows/main.yml`文件，用刚刚创建的构建钩子替换 *YOUR_BUILD_HOOK* 。

    ```
    name: Trigger Netlify Build
    on:
      schedule:
        - cron: '*/15 * * * *' # every 15 mins
    jobs:
      build:
        name: Request Netlify Webhook
        runs-on: ubuntu-latest
        steps:
          - name: Curl request
            run: curl -X POST -d {} YOUR_BUILD_HOOK
    ```

    Enter fullscreen mode Exit fullscreen mode
3.  调整`cron`设置，以确定触发构建的频率。
    `15 8 * * *`会在每天 0815 运行
    `0 0,12 * * *`会在每天中午和午夜运行
    [crontab guru](https://crontab.guru/#0_0,12_*_*_*) 可以帮助你生成正确的 cron 语法。
    参见 [Github 动作](https://help.github.com/en/articles/events-that-trigger-workflows#scheduled-events) …

</article>

[View on GitHub](https://github.com/Jinksi/netlify-build-github-actions)