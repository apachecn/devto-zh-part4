# 用 GitHub 动作和 Laravel 黄昏测试你的 Laravel

> 原文：<https://dev.to/zaher/test-your-laravel-with-github-actions-and-laravel-dusk-3pg1>

> 这篇文章首先发表在我自己的[博客](https://zah.me/2019/09/01/test-your-laravel-with-github-actions-and-laravel-dusk/)上

上周，我获得了新的 GitHub Actions v2.0，这与他们发布的第一个版本有很大不同。您可以将其视为世界级的 CI/CD 解决方案

> GitHub Actions 现在拥有世界一流的 CI/CD，让您轻松实现所有软件工作流程的自动化。直接从 GitHub 构建、测试和部署您的代码。
> 让代码审查、分支管理和问题分类工作按照您想要的方式进行。

我决定深入并测试它，你将获得 2000 分钟的免费时间来测试你的私有库，你甚至可以在多个操作系统上运行你的测试/部署，而不仅仅是 linux，如果你像我一样正在开发一个[电子应用](https://darawish.io/domainbook/?utm_campaign=blogpost&utm_medium=referral&utm_source=devto&utm_content=sharelink)，这是一件大事。

[![Github Actions Pricing](img/6d81cdc9747f231588c482a232bab79c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k4S3cF0A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4nr0ne7ps9rgec1vno2b.png)

你可以从这个[链接](https://github.com/features/actions/signup/?utm_campaign=blogpost&utm_medium=referral&utm_source=zahme&utm_content=sharelink)中阅读更多关于 GitHub 操作和注册测试版的信息。

直到今天，还没有你可以在 PHP 中使用的官方模板，但是多亏了 Franz lied ke T1，你可以使用他的 T2 简单模板 T3，并且随心所欲地修改它。

当然，这不是我今天要讲的，这篇文章主要是解释如何使用 Laravel Dusk 和 GitHub Actions 来测试你的 Laravel 应用程序。我不会列出太多的信息，相反，我将首先解释你如何使用 GitHub 动作，然后我将使用我自己的 docker 映像，它是我专门为 Laravel Dusk 构建的。

## 简介

理解 GitHub Actions 的[核心概念对你来说很重要，但是列出它们超出了本文的范围。我们鼓励你从](https://help.github.com/en/articles/about-github-actions#core-concepts-for-github-actions) [GitHub 文档](https://help.github.com/en/articles/about-github-actions#core-concepts-for-github-actions)中阅读这些概念。

现在我们已经有了我们需要的所有知识，让我们构建我们的第一个 **GitHub 动作工作流**。

## 先决条件

在构建我们的第一个工作流之前，你需要确保你的帐户可以访问新的 GitHub 操作，如上所述，你可以从这个[链接](https://github.com/features/actions/signup/)注册 beta 访问。
如果他们在你的账户上启用了它，你会得到这样一条信息:

> 您的帐户已经启用了 GitHub Actions！今天就去你的某个仓库测试一下吧。

否则，消息会说:

> 这个帐户已经在 GitHub Actions Beta 的等候名单上了！在您的帐户上启用后，我们会通知您。确保您的主要电子邮件地址是最新的，以便我们可以联系到您。

## 创建我们的首个工作流

如文档中所述，您应该在路径`.github/workflows/ci.yml`中创建一个文件，该文件将包含我们的工作流代码。

基本上，一个工作流文件将有 3 个主要的顶级单词:

1.  **名称**:工作流的名称。
2.  **on** :导致工作流运行的动作。
3.  **作业**:工作流将要执行的所有作业的列表。

并且工作流应该至少有一个作业才能运行。

我们的第一个工作流将使用 PHPUnit 执行一个简单的作业来测试我们的 Laravel 应用程序，如果您检查了 Franz
comment，您会注意到这是他的 comment:
的一个简单版本

```
name: CI

on: [push]

jobs:
  phpunit: # name of the job
    runs-on: ubuntu-latest # the host operating system
    steps:
        - uses: actions/checkout@v1 # the action to use before running anything
        - name: Install Composer dependencies # the name of the step
          run: composer install --prefer-dist # the command to run
        - name: Run tests # the name of the second step
          run: php vendor/bin/phpunit # the command to run 
```

上面的代码告诉 GitHub actions 在我们的代码上安装 composer 依赖项和 phpunit，注释解释了
每个关键字。一个小注意，在运行任何作业之前，我发现使用`actions/checkout@v1`动作很重要，因为它将在您的虚拟环境中克隆代码。你可以从这个[链接](https://github.com/actions/checkout/)中了解更多关于结账动作的信息。

## 创建我们的 Laravel Dusk 测试工作流

现在您已经知道了工作流的代码是什么样子，让我们看看如何使用它来测试 Laravel Dusk 测试。
主要的变化将出现在作业部分，因为我假设你想在推送时测试你的代码，但是你可以
自由地查看触发工作流
的[事件，并稍后在你的工作流中实现它们。](https://help.github.com/en/articles/events-that-trigger-workflows#webhook-events)

众所周知，为了测试你的 Laravel 应用程序，你需要一些包和应用程序，比如 google chrome 或者 chromium 和许多 x.org 库。你可以查看我的 [Dockerimage](https://github.com/linuxjuggler/laravel-dusk/blob/master/7.3/Dockerfile)
文件，看看我安装了哪些库。请注意，他们为您提供了许多预装的软件[。](https://help.github.com/en/articles/software-in-virtual-environments-for-github-actions)

> 我刚刚发现你可以在主机上运行测试，不需要提取我自己的 docker 映像，但是我会向你解释你如何使用我的映像，解释[容器](https://help.github.com/en/articles/workflow-syntax-for-github-actions#jobsjob_idcontainer)
> 关键字，以及如何使用本地软件。

要在 docker 映像中运行您的代码，您可以在 jobs 中使用关键字`container`，它会告诉 github 在特定的 docker 映像中运行所有步骤，因为我已经构建了一个映像来测试您的代码，并预装了所有的软件。

相同的 docker 镜像可以用于其他服务，如 [Bitbucket](https://bitbucket.org/) ，你可以在这里查看这篇[文章](https://zah.me/2018/02/03/running-laravel-dusk-tests-on-bitbucket-the-easy-way/)和最近更新的演示代码[。](https://bitbucket.org/zaherg/laravel-dusk/src/master/bitbucket-pipelines.yml)

### 创建作业

概括地说，我们的作业将有一个名称，步骤，我们需要指定主机和 docker 图像。对我来说，我将使用最新版本的 ubuntu，所以代码如下:

```
 dusk-php-latest:
    runs-on: ubuntu-latest
    container: docker://zaherg/laravel-dusk:latest
    steps:
      - uses: actions/checkout@v1
        with:
          fetch-depth: 1
      - name: Prepare the environment
        run: cp .env.example .env
      - name: Install Composer dependencies
        run: |
          composer install --no-progress --no-suggest --prefer-dist --optimize-autoloader
          php artisan key:generate
      - name: Run dusk tests
        run: |
          php artisan dusk:chrome-driver
          nohup bash -c './vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &'
          nohup bash -c 'php artisan serve > /dev/null 2>&1 &'
          php artisan dusk 
```

上述示例将使用 PHP 7.3，因为它是`zaherg/laravel-dusk`图像的最新版本，并且它:

1.  调出 docker 图像
2.  使用 github checkout 动作将代码克隆到我们的 docker 映像中。
3.  创建一个新的`.env`文件。
4.  安装编写器依赖项
5.  为我们的应用程序生成一个密钥。
6.  运行`dusk:chrome-driver`来更新我们的 chromedriver 二进制文件。
7.  在后台运行`chromedriver-linux`。
8.  在后台运行`php artisan serve`,让本地服务器用于我们的测试。
9.  运行我们的`dusk`测试。

如果我们想用 PHP 7.2 测试我们的代码，我们可以只使用`laravel-dusk:7.2`，这样我们的代码将和上面的一样，除了容器行将变成:

```
 container: docker://zaherg/laravel-dusk:7.2 
```

现在，正如我在笔记中提到的，您可以直接在主机虚拟机上运行您的 Laravel Dusk 测试，是的，它更快，默认情况下，他们使用的是 **PHP 7.3** 。对我们所写内容的改动并不大，你只需要把`container`从整个代码中去掉，这样代码就会像下面这样:

```
 dusk-host:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
        with:
          fetch-depth: 1
      - name: Prepare the environment
        run: cp .env.example .env
      - name: Install Composer dependencies
        run: |
          composer install --no-progress --no-suggest --prefer-dist --optimize-autoloader
          php artisan key:generate
      - name: Run dusk tests
        run: |
          php artisan dusk:chrome-driver
          ./vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &
          php artisan serve > /dev/null 2>&1 &
          php artisan dusk 
```

概括地说，有两种方法可以使用 GitHub Actions 和 Laravel Dusk 测试 Laravel 应用程序，要么在主机上，要么使用定制的 docker 映像。同样，与我们在代码中使用`container`的方式一样，您可以将它用于您想要的任何其他 docker 图像。

## 最终工作流文件

这是我解释过的工作流文件，它涵盖了所有情况:

```
name: CI

on: [push]

jobs:

  dusk-php-latest:
    runs-on: ubuntu-latest
    container: docker://zaherg/laravel-dusk:latest
    steps:
      - uses: actions/checkout@v1
        with:
          fetch-depth: 1
      - name: Prepare the environment
        run: cp .env.example .env
      - name: Install Composer dependencies
        run: composer install --no-progress --no-suggest --prefer-dist --optimize-autoloader
      - name: Install Composer dependencies
        run: php artisan key:generate
      - name: Upgrade chrome driver
        run: php artisan dusk:chrome-driver
      - name: Start Chrome Driver
        run: ./vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &
      - name: Run Laravel Server
        run: php artisan serve > /dev/null 2>&1 &
      - name: Run dusk tests
        run: php artisan dusk

  dusk-php-73:
    runs-on: ubuntu-latest
    container: docker://zaherg/laravel-dusk:7.3
    steps:
      - uses: actions/checkout@v1
        with:
          fetch-depth: 1
      - name: Prepare the environment
        run: cp .env.example .env
      - name: Install Composer dependencies
        run: composer install --no-progress --no-suggest --prefer-dist --optimize-autoloader
      - name: Install Composer dependencies
        run: php artisan key:generate
      - name: Upgrade chrome driver
        run: php artisan dusk:chrome-driver
      - name: Start Chrome Driver
        run: ./vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &
      - name: Run Laravel Server
        run: php artisan serve > /dev/null 2>&1 &
      - name: Run dusk tests
        run: php artisan dusk

  dusk-php-72:
    runs-on: ubuntu-latest
    container: docker://zaherg/laravel-dusk:7.2
    steps:
      - uses: actions/checkout@v1
        with:
          fetch-depth: 1
      - name: Prepare the environment
        run: cp .env.example .env
      - name: Install Composer dependencies
        run: composer install --no-progress --no-suggest --prefer-dist --optimize-autoloader
      - name: Install Composer dependencies
        run: php artisan key:generate
      - name: Upgrade chrome driver
        run: php artisan dusk:chrome-driver
      - name: Start Chrome Driver
        run: ./vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &
      - name: Run Laravel Server
        run: php artisan serve > /dev/null 2>&1 &
      - name: Run dusk tests
        run: php artisan dusk

  dusk-host:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
        with:
          fetch-depth: 1
      - name: PHP version
        run: php -v
      - name: Prepare the environment
        run: cp .env.example .env
      - name: Install Composer dependencies
        run: composer install --no-progress --no-suggest --prefer-dist --optimize-autoloader
      - name: Install Composer dependencies
        run: php artisan key:generate
      - name: Upgrade chrome driver
        run: php artisan dusk:chrome-driver
      - name: Start Chrome Driver
        run: ./vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &
      - name: Run Laravel Server
        run: php artisan serve > /dev/null 2>&1 &
      - name: Run dusk tests
        run: php artisan dusk 
```