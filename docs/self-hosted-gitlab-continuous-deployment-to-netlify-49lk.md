# 自托管 Gitlab 持续部署到网络

> 原文：<https://dev.to/sidhantpanda/self-hosted-gitlab-continuous-deployment-to-netlify-49lk>

如果你还没有退房，你一定要退房！

虽然 Netlify 与 Github、Bitbucket 和托管 Gitlab 服务直接集成，但如果您有自己的 Gitlab 实例，事情会变得混乱。这篇文章是为了帮助任何想写 Gitlab 管道的人从 Gitlab 的 CI/CD 工具直接将他们的网站部署到 Netlify。

### 第一步:获取您的网络个人访问权限

前往[用户设置>应用>个人访问令牌](https://app.netlify.com/user/applications#personal-access-tokens)并生成新的访问令牌。你把“Gitlab CD”作为你令牌的描述。一旦生成，请确保将访问令牌复制并保存在文件或活动编辑器窗口中。

### 第二步:获取您的网站 API ID

接下来，在 Netlify Dashboard 中转到您站点的设置页面，可以复制`API ID`的值。

### 步骤 3:将变量添加到您的 Gitlab CI/CD 设置中

在您的自托管 Gitlab 实例上打开 repo，然后进入设置> CI/CD。

*   在变量名`NETLIFY_AUTH_TOKEN`下添加步骤 1 中的访问令牌
*   在变量名`NETLIFY_SITE_ID`下添加步骤 2 中的 API ID。

[![Screenshot-2019-08-27-at-1-39-36-AM](img/859bf263cf5d0131b873f121d1cf9b2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HBD2LVFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/WBL0zsb/Screenshot-2019-08-27-at-1-39-36-AM.png)

### 第四步:给你的回购添加一个`.gitlab-ci.yml`文件

将 Gitlab CI 文件添加到您的 repo 中。下面是一个基本模板，供您开始使用:

```
stages:
  - deploy

deploy:
  stage: deploy
  environment:
    name: production
    url: https://your.website.com
  only:
    - master
  script:
    - npm i
    # your build command
    - npm run build
    - npx netlify-cli deploy --site $NETLIFY_SITE_ID --auth $NETLIFY_AUTH_TOKEN --prod 
```

### 第五步:将名为`netlify.toml`的文件添加到您的 repo 中

这个文件包含需要推进到 Netlify 的目录(构建项目的目录)。一般取名为`build`或者`dist`。

```
[build]
  publish = "build" 
```

就是这样！现在只需提交这些更改，然后点击`master`，就能看到您的网站在 Netlify 上部署完毕:)