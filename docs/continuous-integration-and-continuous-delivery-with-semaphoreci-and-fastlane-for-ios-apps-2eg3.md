# 持续集成和持续交付 iOS 应用的 SemaphoreCI 和浪子

> 原文：<https://dev.to/amarildo/continuous-integration-and-continuous-delivery-with-semaphoreci-and-fastlane-for-ios-apps-2eg3>

当你开发移动应用程序时，**你使用 **Xcode 管理器**手动管理应用程序发布的过程，遵循一些步骤，如签名、测试、构建、存档和部署你的构建，然后改变版本并一次又一次地将新的构建发送到 AppStore。**

如果你每天都生成你的构建，**这个过程是乏味和累人的**。因此，或早或晚，你会问自己:**——我怎样才能使所有这些过程自动化？**这个问题的答案对您的开发和部署流程至关重要，在部署您的构建时，有一个快速的反馈循环来迭代是至关重要的。

使用 SemaphoreCI 和浪子是高速构建产品的最佳选择。每当您将代码推送到远程存储库时，SemaphoreCI 将自动运行您的定制构建、测试和部署管道。

因此，让我们开始进行一些配置，并建立我们的第一个持续集成流程。

## 介绍项目

我们将在本教程中遵循的使用持续集成管道的步骤如下所示:

**1。** **克隆 Github**
**2 的 SemaphoreCI Swift 演示项目。** **配置信号管道**
**3。** **了解我们项目中文件的配置**
**4。** **用持续集成推动我们的第一次构建**

## 从 Github 克隆 SemaphoreCI Swift 演示项目

为了继续本教程，您应该从 GitHub 获取 SemaphoreCI 演示项目的副本，并将其克隆到您的计算机上。

我们将沿着教程使用以下工具:
**1。饭桶**
**2。Xcode**
**3。浪子**
**4。语义**

```
git clone git@github.com:semaphoreci-demos/semaphore-demo-ios-swift-xcode.git 
```

Enter fullscreen mode Exit fullscreen mode

**注:**我向你推荐，**分叉项目**，从你分叉的回购中克隆。

## 配置信号管道

去你的 SemaphoreCI 仪表板，然后**创建一个新项目**。选择一个存储库`semaphore-demo-ios-swift-xcode`并连接到 SemaphoreCI。

[![](img/904b0204a6361166989307f4dc733e9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vx2rUrML--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amarildolucas.s3-sa-east-1.amazonaws.com/SemaphoreCI/Captura%2Bde%2BTela%2B2019-08-12%2B%25C3%25A0s%2B09.42.26.png)

此时，您将看到一个空的连接项目。这是因为我们还没有向项目推送任何东西。

[![](img/234f1237392107d7a3029afa54e609d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G9ChD-wQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amarildolucas.s3-sa-east-1.amazonaws.com/SemaphoreCI/Captura%2Bde%2BTela%2B2019-08-12%2B%25C3%25A0s%2B11.18.34.png)

在您的克隆项目上，打开`.semaphore/semaphore.yml`文件。这是每次推送代码时运行管道的配置文件。文件评论的很好，需要根据自己的需要修改。

**。semaphore/semaphore.yml**

```
# Use the latest stable version of Semaphore 2.0 YML syntax:
version: v1.0

# Name your pipeline. In case you connect multiple pipelines with promotions,
# the name will help you differentiate between, for example, a CI build phase
# and delivery phases.
name: Semaphore iOS Swift example with Fastlane

# An agent defines the environment in which your code runs.
# It is a combination of one of available machine types and operating
# system images.
# See https://docs.semaphoreci.com/article/20-machine-types
# and https://docs.semaphoreci.com/article/120-macos-mojave-image
agent:
  machine:
    type: a1-standard-4
    os_image: macos-mojave

# Blocks are the heart of a pipeline and are executed sequentially.
# Each block has a task that defines one or more jobs. Jobs define the
# commands to execute.
# See https://docs.semaphoreci.com/article/62-concepts
blocks:
  - name: Run tests
    task:
      # Set environment variables that your project requires.
      # See https://docs.semaphoreci.com/article/66-environment-variables-and-secrets
      env_vars:
        - name: LANG
          value: en_US.UTF-8
      prologue:
        commands:
          # Download source code from GitHub:
          - checkout

          # Restore dependencies from cache. This command will not fail in
          # case of a cache miss. In case of a cache hit, bundle  install will
          # complete in about a second.
          # For more info on caching, see https://docs.semaphoreci.com/article/68-caching-dependencies
          - cache restore gems-$SEMAPHORE_GIT_BRANCH-$(checksum Gemfile.lock),gems-$SEMAPHORE_GIT_BRANCH-,gems-master-
          - bundle install --path vendor/bundle
          - cache store gems-$SEMAPHORE_GIT_BRANCH-$(checksum Gemfile.lock) vendor/bundle
      jobs:
        - name: Fastlane test
          commands:
            # Select an Xcode version, for available versions
            # See https://docs.semaphoreci.com/article/120-macos-mojave-image
            - bundle exec xcversion select 10.3
            # Run tests of iOS and Mac app on a simulator or connected device
            # See https://docs.fastlane.tools/actions/scan/
            - bundle exec fastlane test

  - name: Build app
    task:
      env_vars:
        - name: LANG
          value: en_US.UTF-8
      prologue:
        commands:
          - checkout
          - cache restore gems-$SEMAPHORE_GIT_BRANCH-$(checksum Gemfile.lock),gems-$SEMAPHORE_GIT_BRANCH-,gems-master-
          - bundle install --path vendor/bundle
          - cache store gems-$SEMAPHORE_GIT_BRANCH-$(checksum Gemfile.lock) vendor/bundle
      jobs:
        - name: Fastlane build
          commands:
            - bundle exec xcversion select 10.3
            # Gym builds and packages iOS apps.
            # See https://docs.fastlane.tools/actions/build_app/
            - bundle exec fastlane release 
```

Enter fullscreen mode Exit fullscreen mode

如果您试图在此时推送您的代码，您的管道还不能工作，并且会失败。您需要**配置您的密钥和秘密**以允许 SemaphoreCI 与您的项目集成。

要将部署密钥添加到信号量项目中，您需要创建秘密以添加到管道配置文件中。因此，让我们将部署密钥作为一个秘密文件存储在 SemaphoreCI 环境中。

如果您还没有安装我们在本教程中使用的 sem 命令行工具，这是安装它的好时机，请参见 [sem 参考资料](https://docs.semaphoreci.com/article/53-sem-reference)并按照说明进行操作。

```
$ sem create secret your-fastlane-ios-certificates-repo -f id_rsa_semaphoreci:/Users/semaphore/.keys/your-fastlane-ios-certificates-repo 
```

Enter fullscreen mode Exit fullscreen mode

您将在终端上看到以下消息，`Secret ‘fastlane-ios-certificates-repo’ created.`。这意味着一切都按预期运行。

这是 SemaphoreCI 的 ssh 文件。将它上传到 GitHub，这样您就可以部署您的代码，不会再有问题了。

在我们的例子中:

```
ssh-keygen -t rsa -f id_rsa_semaphoreci
cd ~/.ssh/id_rsa_semaphoreci.pub 
```

Enter fullscreen mode Exit fullscreen mode

**复制并粘贴到 Github SSH 密钥配置中。**

接下来，**添加证书存储库的 URL 和加密密码作为环境变量**，它们将在 SemaphoreCI 中被访问。我还建议将 App Store 开发者账户的凭证添加到同一个秘密中。

```
$ sem create secret fastlane-env \
  -e MATCH_GIT_URL=“<your ssh git url>” \
  -e MATCH_PASSWORD=“<password for decryption>” \
  -e FASTLANE_USER=“<App Store developer’s Apple ID>” \
  -e FASTLANE_PASSWORD=“<App Store developer’s password>” 
```

Enter fullscreen mode Exit fullscreen mode

运行此命令后，您将在 SemaphoreCI dashboard 的配置机密中找到您的密钥。

[![](img/6d3785dde270e4be19f1e45bec5c49a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vWZfOYr5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amarildolucas.s3-sa-east-1.amazonaws.com/SemaphoreCI/Captura%2Bde%2BTela%2B2019-08-12%2B%25C3%25A0s%2B10.37.36.png)

用您的密钥和秘密更新您的`.semaphore/semaphore.yml`文件。

```
jobs:
        - name: Fastlane build
        commands:
            - chmod 0600 ~/.keys/*
            - ssh-add ~/.keys/*
            - bundle exec fastlane release
secrets:
        - name: fastlane-env
        - name: fastlane-ios-certificates-repo 
```

Enter fullscreen mode Exit fullscreen mode

## 了解浪子的配置

当您打开演示项目文件夹时，您会看到您有一个 fastlane 文件夹，里面有一些文件。这个文件夹是在我们第一次安装并运行 fastlane 之后添加的。要了解更多关于 fastlane 安装和配置的过程，您应该阅读这篇[介绍性文章](https://docs.fastlane.tools/getting-started/ios/setup/)。

让我们按照您通常会采取的步骤来进行。

**1。安装快车道**
**2。配置快车道文件**
**3。测试是否一切正常**

正如 fastlane 在他们自己的网站上描述的那样:

> 浪子是自动化 iOS 应用测试版部署和发布的最简单方式。它处理所有繁琐的任务，比如生成屏幕截图、处理代码签名和发布应用程序。

它还支持其他平台。

如果您还没有安装，请安装最新的 Xcode 命令行工具。

```
xcode-select —install 
```

Enter fullscreen mode Exit fullscreen mode

安装快车道。

```
# Using RubyGems
sudo gem install fastlane -NV

# Alternatively using Homebrew
brew cask install fastlane 
```

Enter fullscreen mode Exit fullscreen mode

浪子安装后，运行以下命令。

```
fastlane init 
```

Enter fullscreen mode Exit fullscreen mode

**浪子会自动检测你的项目**并询问任何缺失的信息。在我们的例子中，fastlane 安装的这些文件夹和文件存在于我们从 Github 中的演示项目下载的时候。根据您选择的安装类型，系统会为您设置不同的文件。

最有趣的文件是`fastlane/Fastfile`，它包含了发布应用程序所需的所有信息。

我们的快车道当前文件夹。同样，这些文件都有很好的注释，所以我不会对它们中的每一个做进一步的解释。

[![](img/100fdc28adaaacfafe750ff98c495987.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qAg5R3C6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amarildolucas.s3-sa-east-1.amazonaws.com/SemaphoreCI/Captura%2Bde%2BTela%2B2019-08-12%2B%25C3%25A0s%2B09.34.37.png)

**快车道/应用文件**

```
# For more information about the Appfile, see:
#     https://docs.fastlane.tools/advanced/#appfile

app_identifier "***" # The bundle identifier of your app
apple_id "***" # Your Apple email address
team_id "***" # Developer Portal Team ID 
```

Enter fullscreen mode Exit fullscreen mode

**fastlane/Fastfile**

```
# This file contains the fastlane.tools configuration
# You can find the documentation at https://docs.fastlane.tools
#
# For a list of all available actions, check out
#
#     https://docs.fastlane.tools/actions
#
# For a list of all available plugins, check out
#
#     https://docs.fastlane.tools/plugins/available-plugins
#

# Uncomment the line if you want fastlane to automatically update itself
# update_fastlane

default_platform(:ios)

platform :ios do

  before_all do
    # installed via the semaphore plugin with `fastlane add_plugin semaphore`
    setup_semaphore
  end

  desc "Run Tests"
  lane :test do
    scan
  end

  desc "Deploy a new version to the App Store"
  lane :release do
    match(type: "appstore")
    gym(export_method: "app-store") # Build your app - more options available
    deliver(force: true)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

**快车道/健身房**

```
# For more information about this configuration visit
# https://github.com/fastlane/fastlane/tree/master/gym#gymfile
#
# In general, you can use the options available
# fastlane gym --help

# Remove the # in front of the line to enable the option

scheme "HelloWorld"

sdk "iphoneos12.4"

output_directory "./" 
```

Enter fullscreen mode Exit fullscreen mode

**快车道/匹配文件**

```
git_url("https://github.com/amarildolucas/fastlane_certficates")

storage_mode("git")

type("appstore") # The default type, can be: appstore, adhoc, enterprise or development

# app_identifier(["com.amarildolucas.com"])
# username("***") # Your Apple Developer Portal username

# For all available options run `fastlane match --help`
# Remove the # in the beginning of the line to enable the other options

# The docs are available on https://docs.fastlane.tools/actions/match 
```

Enter fullscreen mode Exit fullscreen mode

**快车道/外挂文件**

```
# Autogenerated by fastlane
#
# Ensure this file is checked in to source control!

gem 'fastlane-plugin-semaphore' 
```

Enter fullscreen mode Exit fullscreen mode

这是我们设置和部署我们的第一个持续集成构建所需要的一切。代码非常具体，不言自明。您刚刚定义了两个不同的通道，一个用于您的测试部署，一个用于 App Store 发布。要在 App Store 发布你的应用，你现在要做的就是推送你的代码。

**注意:**确保在 Xcode 中没有勾选 Xcode“自动管理签名”。并且您的签名(发布)包含所选的匹配 AppStore 预置描述文件。

## 一些注意事项

### Gemfile

建议您使用 Gemfile 来定义您对 fastlane 的依赖性。这将清楚地定义所使用的 fastlane 版本及其依赖项，并且还将加速 fastlane 的使用。

我们当前的 Gemfile:

```
source 'https://rubygems.org'

gem "fastlane"
gem "xcode-install"
gem "cocoapods"

plugins_path = File.join(File.dirname(__FILE__), 'fastlane', 'Pluginfile')
eval_gemfile(plugins_path) if File.exist?(plugins_path) 
```

Enter fullscreen mode Exit fullscreen mode

### 设置浪子比赛

您可以使用 fastlane match 来管理代码签名。我们在快速通道设置中使用它。

要设置匹配，您需要一个私有的 Git 存储库。遵循[文档](https://docs.fastlane.tools/actions/match/)，从我们项目的根目录运行 init 命令，并遵循终端中显示的指令。

```
bundle exec fastlane match init 
```

Enter fullscreen mode Exit fullscreen mode

在我们的例子中，我们的匹配文件存在于 Github 中的克隆演示项目中。因此，在没有 init 的情况下运行`fastlane match`,以获得安装到项目中的所有必需的密钥、证书和配置文件。🙌

**注:**

> API 密钥或部署凭证等私有信息不应写入管道定义文件或提交给源代码控制的其他地方。在 Semaphore 上，您使用 sem CLI 或通过 web 界面(侧栏的配置部分- >机密)将这些值定义为*机密*。秘密由组织中的所有项目共享。 **—旗语队**

构建您的项目并运行 fastlane 来检查我们到目前为止所做的一切是否如预期的那样工作。

[![](img/0dc7299098979ef838f3c418d2cec5df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--csBT4VRv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amarildolucas.s3-sa-east-1.amazonaws.com/SemaphoreCI/Captura%2Bde%2BTela%2B2019-08-12%2B%25C3%25A0s%2B10.26.41.png)

如果你看到消息`[10:28:38]: fastlane.tools finished successfully 🎉`，庆祝吧🎉。

现在，我们只需要将我们的代码推送到 GitHub，SemaphoreCI 将为我们运行 CI/CD 管道。

```
git add -A
git commit -m "setup semaphore"
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

将代码推送到 GitHub 中的 master branch 后，信号量管道将第一次运行。如果您单击 commit 链接，您可以在一个漂亮的布局组件中看到所有过程(步骤),这些组件将您的管道配置的步骤分开。

[![](img/ac8c9ae0cfdc242883aa21cf1bde87c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--38ZjEnLx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amarildolucas.s3-sa-east-1.amazonaws.com/SemaphoreCI/Captura%2Bde%2BTela%2B2019-08-12%2B%25C3%25A0s%2B11.58.59.png)

瞧！现在，您只需不断将代码推送到您的分支，App Store Connect 会处理构建，它将在 TestFlight 中可用，您将能够为您的新 iOS 应用版本选择它。

## 结论

通过自动化部署，您可以做更多的事情。CI/CD 管道有很多优点，比我们通过本教程遵循的步骤更多。您可以向您的管道配置添加更多步骤，如获取您的 CHANGELOG 文件、将消息推送到 Slack、自动增加内部版本号、将 dSYM 上传到 Crashlytics 等。

在你花一些时间配置之后，所有这些东西将帮助你专注于编写代码，而不是每次都手动交付你的应用。因此，您的构建更新不会有压力和乏味。您可以通过在过程中添加工具来检查您的代码，例如运行静态代码审查、林挺、测试或任何其他您可以自动化的重要步骤。

然而，先从简单的事情开始。如果你已经做到了这一步，恭喜你！😃我希望这篇文章对你有所帮助。

想要持续交付您的 iOS 应用程序吗？查看[使用 Semaphore CI/CD](https://semaphoreci.com/product/ios) 构建、测试和交付您的 iOS 应用。