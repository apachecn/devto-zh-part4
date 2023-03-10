# 使用 GitLab CI 将 Laravel 部署到 AWS Beanstalk(包括测试和试运行/生产分支)

> 原文：<https://dev.to/mjsarfatti/deploy-laravel-to-aws-beanstalk-with-gitlab-ci-including-testing-and-staging-production-branches-f8g>

*王德·古普塔在 Unsplash 上拍摄的照片*

这是一篇教程，讲述了一天晚上我开始简化我的部署过程，几个小时后凌晨 4 点我发现自己在构建新的 docker 映像。希望它包含有价值的信息，对于那些正在为你的 Laravel 应用程序设置 CI/CD 的人来说，这样你就不用像我一样熬夜了。

## 我们想要完成的事情

很简单，每次推送至`master`或`staging`时，phpunit 测试都会自动运行。

此外，如果分支`staging`上的所有测试都通过，应用程序将自动部署到 Elastic Beanstalk。

我将`master`分支部署保留为“手动”,因为我不希望在生产环境中意外地部署一个新特性。

## 要求

我试图让这篇文章简明扼要，为此，希望您已经准备好了以下内容(如果您没有，不要担心，请将这篇文章加入书签，稍后再来看):

1.  一个在您的本地环境中工作的 Laravel 应用程序，它在 GitLab 上有 git 存储库，有两个分支:`master`和`staging`(到目前为止都很标准)
2.  AWS Elastic Beanstalk 上的两个配置和运行环境(您可能希望它们在同一个 Beanstalk 应用程序中，我发现本指南对本部分非常有用)
3.  您的 AWS 帐户中的 IAM 用户，拥有 Elastic Beanstalk 完全访问权限(我们将需要该用户的访问/密钥，*确保您将它们记在某个地方*
4.  说明你已经安装了[EB CLI](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html)T3】和 ran `eb init`

## 0。严重违约

如果你已经运行了`eb init`，你会注意到现在有一个名为`.elasticbeanstalk`的文件夹。这个文件夹对于 GitLab 来说是必不可少的，但是由于某种原因，这个命令也在你的`.gitignore`中添加了几行，这使得 GitLab 无法完成它的工作。如果你在你的`.gitignore` :
里看到这样的东西

```
# Elastic Beanstalk Files
.elasticbeanstalk/*
!.elasticbeanstalk/*.cfg.yml
!.elasticbeanstalk/*.global.yml 
```

继续删除这些行。

## 1。入门指南

让我们从过程的结尾开始，只是为了增加一点趣味。

在 Laravel 应用程序的每次部署之后(对于任何服务器设置都是如此，不仅仅是 EB ),我们需要确保我们的 remote (gitignored) `/vendor`文件夹包含所有需要的包，并且远程数据库的结构是最新的。这是通过运行两个命令来完成的:`$ composer install`和`$ php artisan migrate`。

此外，在我们的特定设置中，我们需要告诉 EB 我们的应用程序是从文件夹`/public`中提供的，由于 SSL 是**道德上强制的**，现在我们可能还想告诉 Apache 将所有 *http* 请求重定向到 *https* 。

这是一个基本的设置，应该可以很好地为 98%的项目工作。

现在，我们可以 *ssh* 进入远程机器并运行一些命令，但幸运的是，Elastic Beanstalk 为我们提供了配置文件(一组位于`.ebextensions`文件夹中的 YAML 文件，指示 AWS 上的远程服务器在部署前后运行某些命令),这些文件非常适合我们的 CI/CD 工作流。

欢迎您探索官方文档，但再次幸运的是，您真的在 GitHub 上发布了所需的文件供您阅读。

在您的项目运行的根目录中:

```
$ svn export https://github.com/mjsarfatti/laravel-eb-gitlab-ci/trunk/.ebextensions 
```

这将创建一个`.ebextensions`文件夹，里面有四个文件。请随意检查这些文件，注意我已经在其中添加了一些好东西。

## 2。吉泰实验室 YAML

为了指导 GitLab 在部署期间如何运行以及运行什么，我们需要创建并提交一个`.gitlab-ci.yml`文件。GitLab CI 如何工作超出了本教程的范围，但是文件本身是不言自明的。

将其添加到您的项目:

```
$ svn export https://github.com/mjsarfatti/laravel-eb-gitlab-ci/trunk/.gitlab-ci.yml 
```

然后打开这个文件，确保将变量`MYSQL_DATABASE`设置为您的测试数据库名称(您可能会在您的`phpunit.xml`文件中找到它)。

### 有用一点就知道🎁

有时，即使测试失败，您也可能想要部署(例如，如果您正在测试部署本身)。或者您希望进行简单的编辑(比如自述文件或警告消息中的错别字),并且不希望将宝贵的 CI 时间用于无用的测试。

我们的`.gitlab-ci.yml`有一个友好的规则，如果提交消息包含短语`[skip tests]`(或者类似于`[skip test]`或`[skip-tests]`的变体)，它将跳过测试。

## 3。为成功设置 GitLab CI

登录你的 GitLab 账户，在你的项目的*设置* > *CI / CD* 中添加两个变量，一个名为`AWS_ACCESS_KEY_ID` with，你知道了，你的 IAM 用户访问密钥 id，另一个名为`AWS_SECRET_ACCESS_KEY` with...你知道吗？

这就是🥳

推开它，享受你应得的内心平静。

## 哦，我差点忘了

你可能会问，我是如何花了一整晚来构建 docker 图像的？

这一切都是从我发现爱德华多·比扎罗的这些惊人的 Docker 图片开始的，这些图片预先配置了 PHP、Composer、Node 和 Yarn，可用于 PHP 7.1、7.2 和 7.3:[https://github.com/edbizarro/gitlab-ci-pipeline-php/](https://github.com/edbizarro/gitlab-ci-pipeline-php/)

我想，这太疯狂了(仍然只有晚上 11 点)，我会把这些都记在我的`.gitlab-ci.yml`里，然后就到此为止。

LOL。

这些图像不包括`eb`(为什么会包括它们)。所以我试着安装了它。但是`eb`需要`pip`，`pip`需要几个诡异的 Python 包。只是`apt-get`，对吗？不对。

这些图像不能在根用户上运行(我猜这是一个合理的默认设置？)，也就是说我在 CI 期间不能安装任何包。我知道要走的路是分叉项目，潜入他们的 docker 文件，并根据我的需要定制它。但我以前几乎没有用过 Docker，我不喜欢在深夜开始新的冒险(有趣，嗯？).

所以我抗争，抗争，抗争。我尝试了它们附带的所有 3 个 Linux 发行版，并且尝试了所有不同的方法来安装`eb`，包括在本地文件夹中安装所有的工具。什么都没用。

所以现在是凌晨 3 点，我面临着一个简单的选择:我是应该去睡觉休息，还是这是我生命中的一场战斗？

你可以想象发生了什么。

我打开了[https://hub.docker.com/](https://hub.docker.com/)，研究了比扎罗的回购，研究了如何开始，你瞧，一个小时后我就有了:

*   成功地重新配置了比扎罗的映像以安装 EBCli、AWSCli 和 CLI53(为什么不呢)
*   向 Docker 发布新图像
*   成功地使用我的第一个新 Docker 映像将我的 Laravel 应用程序部署到 Elastic Beanstalk🎉🎉🎉

所以如果你注意到在我们的`.gitlab-ci.yml`文件中我们使用了`image: mjsarfatti/gitlab-ci-pipeline-php-aws:latest`。

请注意:这是一个基于 PHP 7.2 Debian 的图像。不能安装包。没有经过彻底的测试。但这很有效。

## 大拜

一如既往，如果不是昨晚陪伴我的材料、软件和教程，我将不会有今天的成就。排名不分先后，大拜:

*   [https://github.com/edbizarro/gitlab-ci-pipeline-php/](https://github.com/edbizarro/gitlab-ci-pipeline-php/)
*   [https://medium . com/@ thimblot/deploying-a-flask-application-on-AWS-with-git lab-ci-CD-part-2-a 175 DC 132950](https://medium.com/@thimblot/deploying-a-flask-application-on-aws-with-gitlab-ci-cd-part-2-a175dc132950)
*   [https://medium . com/dowebthings/how-to-deploy-from-git lab-to-elastic-beanstalk-AWS-cdac7b 701004](https://medium.com/dowebthings/how-to-deploy-from-gitlab-to-elastic-beanstalk-aws-cdac7b701004)
*   [https://blog . powerupcloud . com/deploy-python-application-on-AWS-elastic beanstalk-using-git lab-runner-8982 FB 26 E4 a4](https://blog.powerupcloud.com/deploy-python-application-on-aws-elasticbeanstalk-using-gitlab-runner-8982fb26e4a4)
*   [https://github . com/spiritix/lumen-AWS-git lab-continuous-deployment](https://github.com/spiritix/lumen-aws-gitlab-continuous-deployment)
*   [https://github.com/Cox-Automotive/aws-ebcli](https://github.com/Cox-Automotive/aws-ebcli)
*   [https://github.com/rennokki/laravel-aws-eb](https://github.com/rennokki/laravel-aws-eb)

如果本指南对您有用，或者您遇到任何问题，请在下面评论！T3】

<small>-编辑-
增加了 rennokki/laravel-aws-eb 链接</small>