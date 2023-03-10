# 在 Github Actions 上踢轮胎

> 原文：<https://dev.to/leesmith/kicking-the-tires-on-github-actions-45g9>

这篇文章最初发表在我的博客上。

我使用 GitHub 已经超过 10 年了，我真的认为 Github Actions 是迄今为止最大的功能之一。Github Actions 本质上是一个自动化 API，“使您能够直接在 Github repo 中创建定制的软件开发生命周期工作流”。基于您指定的一些事件(如推送或问题创建或新发布)，您能够编排工作流并让 Github 为您执行它们。工作流由位于存储库根目录下的工作流文件描述。

这有无限的可能性。您可以有一个工作流来分类陈旧的问题，设置代码审查，执行分支管理，或者自动化新的发布。但是动作最流行的用法可能是持续集成和部署(CI/CD)。这意味着 Github 现在提供了原生 CI/CD，这是 Travis CI 和 CircleCI 等第三方供应商多年来一直提供的。Github 动作支持大量的语言、操作系统和框架。他们还提供矩阵构建，您可以在其中跨多个操作系统和语言版本进行并行测试。

这听起来令人兴奋！目前，Github Actions 还在测试阶段，可以免费公开回购，所以我想把我的一个开源 Ruby on Rails 应用程序转换成使用 Github Actions 来运行 push 上的测试套件。测试套件非常小，唯一的主要依赖项是 PostgreSQL。

要开始设置，我的回购至少需要一个工作流文件。工作流文件位于`.github/workflows`目录，为 YAML 格式。

这是我的样子:

```
# ruby.yml

name: Build

on: [push]

jobs:
  build:
    runs-on: ubuntu-18.04

    services:
      postgres:
        image: postgres:11
        ports: ['5432:5432']
        options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5

    steps:
    - uses: actions/checkout@v1
    - name: Set up Ruby 2.6
      uses: actions/setup-ruby@v1
      with:
        ruby-version: 2.6.x
    - name: Build and test with Rake
      env:
        PGHOST: 127.0.0.1
        PGUSER: postgres
        RAILS_ENV: test
      run: |
        sudo apt-get -yqq install libpq-dev
        gem install bundler
        bundle install --jobs 4 --retry 3
        cp config/database.postgres.yml config/database.yml
        bundle exec rake db:test:prepare
        bundle exec rake spec 
```

Enter fullscreen mode Exit fullscreen mode

我将这个工作流命名为“Build ”,并设置它在我推送到 repo 时运行。这个部分确实是动作的核心。作业由在同一虚拟环境中执行的步骤组成。我在这里说，这个作业将在 Ubuntu 18.04 虚拟机上运行，并使用 PostgreSQL 版本设置为 11 的 postgres 服务。这些步骤首先检查我的 repo，然后使用 Github 的两个公共操作建立一个 ruby 环境。接下来，我设置一些环境变量，安装一个开发包，安装我的项目的 gem 依赖项，最后运行测试套件。每次推送到 repo 时，Github 都会为我执行这个工作流，我可以从项目的 Actions 选项卡查看日志，以查看输出。

我真的很兴奋看到他们还添加了什么来增强动作。目前看来，它将是其他 CI/CD 服务的有力竞争者。