# 使用 Google Cloud Build (CI/CD)将 Gatsby 站点部署到 Firebase

> 原文：<https://dev.to/leomercier/deploying-a-gatsby-site-to-firebase-with-google-cloud-build-ci-cd-511c>

我在 Netlify 上看到很多关于托管 Gatsby 的文章，但是没有关于 Firebase 和 Google 云平台的文章，所以我决定写一篇。在下面的配置中，它可以免费托管任何入门项目。

我在伦敦的数字产品工作室 Crowdform 管理开发团队，我们一直忙于在盖茨比建立我们最新的客户网站。

作为一家代理公司，我们在较大的数字产品中选择了 ReactJS 和 GraphQL，因此为了保持一致性，我们也选择了 Gatsby。Gatsby 可以用来建立静态网站，这些网站是进步的网络应用程序，它们遵循最新的网络标准，并经过优化，具有很高的性能，因此在 Google Lighthouse 上得分很高。我们仍然使用 Wordpress，但是现在是一个无头配置。

# 先决条件

1.  Gatsby 项目([遵循快速入门指南](https://www.gatsbyjs.org/docs/quick-start/)
2.  GitHub、Bitbucket 或 Google 云资源库中的项目
3.  启用计费的谷歌云帐户(你不会被收费，因为云构建的限制是慷慨的，Firebase 托管是免费的。云构建为您提供最初的 120 次构建-每天几分钟免费)
4.  [谷歌 Firebase 项目](https://www.crowdform.co.uk/blog/deploying-a-gatsby-site-to-firebase-with-google-cloud-build#firebase) &谷歌云构建 API 启用

# 设置云构建

1.  添加生成触发器
2.  Link a Git Repo

[完整的谷歌云构建指令](https://www.crowdform.co.uk/blog/deploying-a-gatsby-site-to-firebase-with-google-cloud-build#cloudbuild)

# Cloudbuild.yaml 配置

在你的项目的根目录下创建一个文件 cloudbuild.yaml，并将下面的要点复制到 yaml 文件中。

```
steps:
  - name: node:10.15.1
    entrypoint: yarn
    args: ["install"]
  - name: node:10.15.1
    entrypoint: yarn
    args: ["add", "firebase-tools"]
  - name: node:10.15.1
    entrypoint: yarn
    args: ["build"]
  - name: "node:10.15.1"
    entrypoint: "./node_modules/.bin/firebase"
    args: ["deploy", "--project", "$PROJECT_ID", "--token", "$_TOKEN"] 
```

1.  使用纱线和节点虚拟机
2.  安装所有依赖项
3.  安装 Firebase-cli(通常全局安装在本地计算机上)
4.  运行 Gatsby 构建函数并生成静态站点到/public
5.  将公共文件夹的内容推送到 Firebase 主机。

# 授权 Firebase

`firebase login:ci`

在 CloudBuild 的“替换变量”下添加键值。

`_TOKEN: {token from firebase login:ci}`

现在运行构建触发器

要获得完整的分步指南，请参阅我们的文章[使用云构建将 gatsby 站点部署到 Firebase】。](https://www.crowdform.co.uk/blog/deploying-a-gatsby-site-to-firebase-with-google-cloud-build)