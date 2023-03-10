# 如何在 Gitlab CI 中将 Python 项目部署到 Heroku

> 原文：<https://dev.to/michaelyin/how-to-deploy-python-project-to-heroku-in-gitlab-ci-47la>

## 简介

在本 Heroku 教程中，我将讲述如何在 Gitlab CI 中将 python 项目部署到 Heroku

读完后，你会得到:

1.  如何创建 Heroku 令牌来访问 Heroku 平台 API？

2.  如何使用 Heroku CLI 将项目部署到 Heroku

3.  如何使用`dpl`包将项目部署到 Heroku。

## 注册 Heroku API 令牌

首先，我们应该创建一个`Heroku token`，然后我们可以使用这个令牌调用一些 Heroku API 来部署 Python 项目。

我们创建 Heroku API 令牌主要有两种方式。

1.  请进入[仓库账户设置](https://dashboard.heroku.com/account)

2.  在底部，您可以看到一个部分`API Key`，您可以在那里生成 API 令牌。

另一种方法是在 Heroku CLI 中创建 API。

```
USAGE
  $ heroku authorizations:create

OPTIONS
  -S, --short                    only output token
  -d, --description=description  set a custom authorization description

  -e, --expires-in=expires-in    set expiration in seconds (default no
                                 expiration)

  -j, --json                     output in json format

  -s, --scope=scope              set custom OAuth scopes

DESCRIPTION
  This creates an authorization with access to your Heroku account. 
```

```
$ heroku authorizations:create -d "token for Django project"

# after you create, you can check existing tokens with this command
$ heroku authorizations 
```

在帐户设置页面和 Heroku CLI 中创建令牌有一些小的区别

1.  如果您更改了 Heroku 帐户密码，在帐户设置页面创建的令牌将会过期。

2.  您可以创建多个令牌(一个项目一个令牌)并使用 Heroku CLI 添加描述。

所以我建议使用 Heroku CLI 为您的 Heorku API 创建令牌。

## 在 Gitlab CI 中使用 Heroku API 令牌

有两种方法可以做到这一点，你应该在你的项目的根目录下创建`.gitlab-ci.yml`, git lab 会自动扫描它。

一种方法是安装 Heroku CLI 并配置 git repo。

```
deploy:
  image: debian:stretch
  stage: deploy
  variables:
    HEROKU_APP: django
    HEROKU_DEPLOYMENT_BRANCH: master
  only:
    - master
  before_script:
    - apt-get update -y
    - apt-get install -y curl git gnupg
    - curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
    - |
      cat >~/.netrc <<EOF
      machine api.heroku.com
        login $HEROKU_EMAIL
        password $HEROKU_TOKEN
      machine git.heroku.com
        login $HEROKU_EMAIL
        password $HEROKU_TOKEN
      EOF
    - chmod 600 ~/.netrc
    - heroku git:remote --app $HEROKU_APP
  script:
    - git checkout master
    - git push heroku master:$HEROKU_DEPLOYMENT_BRANCH 
```

1.  你需要在 Gitlab CI env 变量中设置`$HEROKU_EMAIL`和`$HEROKU_TOKEN`才能让它工作。

2.  你需要把`$HEROKU_APP`换成你的 Heroku app。

3.  我们安装了 Heroku CLI，然后在~/中安装了`$HEROKU_TOKEN`。netrc 可以使 Heroku CLI 可以访问 Heroku API。

另一种方法是使用一个包`dpl`来做这件事。

```
deploy:
  stage: deploy
  variables:
    HEROKU_APP: django
  only:
    - master
  script:
    - gem install dpl
    - dpl --provider=heroku --app=$HEROKU_APP --api-key=$HEROKU_TOKEN 
```

在您将`.gitlab-ci.yml`推送到 Gitlab repo 的主分支之后，对主分支的新提交将被自动部署到 Heroku。因此您不需要在本地 env 中部署。

## 结论

在这篇 Heroku 教程中，我谈到了如何在 Gitlab CI 中将项目部署到 Heroku，您可以更改配置代码以使其在其他 CI 上工作。

如果您有任何问题，请随时联系我们。

## 引用

*   heroku 授权:创建

本文最初发布在 Gitlab CI 中的[如何将 Python 项目部署到 Heroku](https://www.accordbox.com/blog/how-deploy-python-project-heroku-gitlab-ci/)