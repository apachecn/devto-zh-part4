# 如何用 Selenium 测试你的 Sylius 插件？

> 原文：<https://dev.to/kayneth/how-to-test-your-sylius-plugins-with-selenium-38ci>

在 [Tataragne Interactive](https://www.tataragne.com/) ，我们经常通过插件开发 Sylius 的功能。根据 Javascript 函数，我们将 Behat 和 Selenium 一起用于场景。

> Behat 是一个开源的 PHP 行为驱动开发框架。BDD 是一种通过开发人员和业务之间持续的基于实例的交流来开发软件的方法，该应用程序支持这种方法。场景是用一种叫做小黄瓜的特殊格式编写的。

通过这篇博文，您将了解如何配置 Sylius 插件项目来使用 Selenium。我认为你使用的是 Sylius 插件框架。

## 添加硒到我们的 docker-compose 配置

首先，我们需要一个 chrome-node(或者 firefox)来工作。我们将使用两个集装箱。一个用于集线器，一个用于节点。Hub 是运行所有测试的机器，但是代码将在不同的节点上执行，如 Selenium 文档中所述。

```
# docker-compose.yaml
version: '3.7'

services:
    plugin: # Your app configuration. This is not the subject here

    hub:
        image: selenium/hub:3.11.0
        ports:
            - 4444:4444
        links:
            - plugin

    chrome:
        image: selenium/node-chrome:3.11.0
        #  You can also use this image and open the  container port to one of your host port to be able to use a VNC viewer
        # image: selenium/node-chrome-debug:3.11.0
        depends_on:
            - hub
        links:
            - plugin
        environment:
            - HUB_HOST=hub
            - HUB_PORT=4444
        # ports:
        #     - 5900:5900 
```

Enter fullscreen mode Exit fullscreen mode

* * *

有了这个配置，您将能够运行`docker-compose up -d`并轻松地使用您的`behat.yaml`配置。

## 你需要在 behat.yml 中做什么改变才能运行 Selenium

对 Behat 配置的更改非常简单。

首先，你可以更改`Behat\MinkExtension`的`base_url`，这取决于你之前为应用程序设置的内容。对我来说，in 可能只是简单地被`"http://plugin:80/"`以我的容器为目标(不安全😅)端口 80。

其次，你必须将你的 Hub 主机添加到你的 chrome 会话中。简单地在 chrome 会话的`selenium2`配置中添加一个键`wd_host`。

第三，你可以在能力中设置`javascriptEnabled`到`true`。

遵循下面的配置示例。

```
Behat\MinkExtension:
    files_path: "%paths.base%/vendor/sylius/sylius/src/Sylius/Behat/Resources/fixtures/"
    base_url: "http://plugin:80/"  # Adapt this
    default_session: symfony
    javascript_session: chrome
    sessions:
        symfony:
            symfony: ~
        chrome:
            selenium2:
                browser: chrome
                wd_host: http://hub:4444/wd/hub # Add this
                capabilities:
                    browserName: chrome
                    browser: chrome
                    version: ""
                    marionette: null # https://github.com/Behat/MinkExtension/pull/311
                    javascriptEnabled: true  # Add this
                    chrome:
                        switches:
                            - "start-fullscreen"
                            - "start-maximized"
                            - "no-sandbox" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

🎉🎉🎉您的配置现在已经就绪🎉🎉🎉

正如本期 SymfonyExtension 所述，你可能会遇到一个`Session "symfony" is not registered.`。您可以在退款插件中关注 [#138 拉式请求的变化。](https://github.com/Sylius/RefundPlugin/pull/138/files)

我希望这篇博文能帮助你在 Sylius 插件中配置你的管理或商店场景。我希望能够在以后的文章中讨论在 Gitlab CI 作业中运行这些测试。💪