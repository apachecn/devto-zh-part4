# 如何用 Travis CI 加密多个文件

> 原文：<https://dev.to/mmphego/how-to-encrypt-multiple-files-with-travis-ci-5a15>

[![post image](img/d32f1984265e2847ace1a4b3ec769f08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HlHin_0L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/master/img/new_post_word_cloud.jpg)

*最初发布于[blog.mphomphego.co.za](https://blog.mphomphego.co.za/blog/2019/07/25/How-to-encrypt-multiple-files-with-Travis-CI.html)2019 年 7 月 25 日。*

* * *

# 故事

我一直在不断改进我的一个副业项目，抓取电子商务网站，提取一些数据，然后将数据上传到谷歌表。我最近的更新之一是添加电子邮件通知，但由于我使用[特拉维斯 CI](//travis-ci.com) 作为 [cron-job](https://docs.travis-ci.com/user/cron-jobs/) 运行脚本，我需要加密我的 [Google Dev](https://developers.google.com/) `client_secret.json`文件(原因很明显)以及我的新电子邮件配置文件，以便特拉维斯 CI 在公共平台上运行我的包含敏感信息的脚本。

然而，Travis CI 不支持多个文件加密，这让我花了一段时间才意识到...
[![image](img/d3d6f2d43a2927469491fd590c18ca19.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--6oyQo4kz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/master/assets/travis_error.png)

在这一点上，我甚至停止了计算失败的构建。
[![image](img/4bfab4c02e2566c65893ad4df2dc97fa.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--CEg_yyZp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/master/assets/travis_error2.png)

**注意:**如果您使用 Travis CI 客户端来加密多个文件，它会覆盖加密的条目，这就是为什么我的脚本一直无法构建的原因。

在本帖中，我将详细介绍一种使用 CLI 客户机在 Travis CI 上加密多个文件的解决方法。

如果你想了解这个项目，请点击这里。

# 该如何如何

在继续之前，我们需要安装一些依赖项。

**注意:**这些指令假设你正在运行`Ubuntu 18.04`。

## 安装

你需要安装`travis-ci cli client`，按照这个[安装指南](https://github.com/travis-ci/travis.rb#installation)。

**TL；DR:** 在你的 Ubuntu 安装上，否则继续自担风险。

运行以下命令:

```
$ sudo apt update
$ sudo apt-get install ruby-full
$ gem install travis 
```

Enter fullscreen mode Exit fullscreen mode

如果像我一样，你不喜欢在你的系统中安装软件包。
我有一个[Docker 文件](https://www.docker.com/)，它构建了一个 Docker 容器，你可以很容易地运行`travis client`。

[点击此处](http://bit.ly/2YslZG6)获取详细的安装说明。

## 测试

验证安装完成后，运行:`travis version`

一旦我们成功安装，[使用您的 GitHub 用户名&密码或](https://github.com/travis-ci/travis.rb#login)[令牌](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)详细信息登录 Travis。

```
$ travis login --com 
```

Enter fullscreen mode Exit fullscreen mode

# 走查

如果你需要加密多个文件，首先我们需要创建一个所有敏感文件的`archive`，加密它，版本控制它，然后在构建期间解密它。

我需要加密我的敏感的`email_config.ini`和`client_secret.json`文件，我就是这样做的。

```
$ tar cvf secrets.tar email_config.ini client_secret.json
# Adding `--add` arg automatically adds the decryption command to your .travis.yml
$ travis encrypt-file secrets.tar --add --com
$ git add secrets.tar.enc .travis.yml
$ git commit -m 'Archiving email config and client secret into secret.tar file.'
$ git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

在您的`.travis.yml`中，您应该注意到一个新命令`openssl ...`这个命令解密您的`secrets.tar`文件，然后您将不得不添加一个命令来提取文件。

```
before_install:
  - openssl aes-256-cbc -K $encrypted_*******_key -iv $encrypted_*******_iv -in secrets.tar.enc -out secrets.tar -d
  - tar xvf secrets.tar
script:
  - price_checker.py --email ./email_config.ini --json ./client_secret.json -s "Shopping List" --update 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，下面是我成功构建 Travis 的截图。

[![image](img/ed3485b0d9204fa426366c0e4b9d0fd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zUmvOeKi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/master/assets/travis_success.png)

# 引用

*   [特拉维斯的客户](https://github.com/travis-ci/travis.rb)
*   [加密文件不能用于多个文件](https://github.com/travis-ci/travis.rb/issues/239)
*   [加密文件](https://docs.travis-ci.com/user/encrypting-files/)