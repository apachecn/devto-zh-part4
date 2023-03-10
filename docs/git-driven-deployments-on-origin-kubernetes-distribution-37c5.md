# Git 驱动的原始 Kubernetes 发行版部署

> 原文：<https://dev.to/simbo1905/git-driven-deployments-on-origin-kubernetes-distribution-37c5>

这是系列文章的第二篇，解释了如何在 Kubernetes 的 OKD 发行版上实现 git 驱动的基础设施即代码。我们将我们的工具开源为 [OCD](https://github.com/ocd-scm/ocd-meta) 。他们可以被用作火箭动力彩虹灰尘，让你的功能出货🌈✨🚀这篇文章将给出一个简短的 [OCD 教程](https://github.com/ocd-scm/ocd-meta/wiki/Short-Tutorial:-Git-Driven-Deployments-On-Minishift)的概述，该教程在你将更改推入 git repo 时部署并升级 ReactJS 应用。这是一个视频，展示了教程让你做的事情:

[https://www.youtube.com/embed/FlJIxfD2Ql4](https://www.youtube.com/embed/FlJIxfD2Ql4)

我们称这种 git 驱动的基础设施为代码。作为额外的一步，教程让你用一个简单的`helm rollback`命令回滚升级。为什么我们认为这是一个如此棒的主意？

[上一篇文章](https://dev.to/simbo1905/git-driven-infrastructure-as-code-on-origin-kubernetes-distribution-4hcj)有一个视频，视频中把一个 git URL 放入 OpenShift，然后它构建代码并部署一个 realworld.io ReactJS 应用。你在视频中发现了最终削弱团队力量的原因吗？我将 web 控制台与🐁或者🐾垫。这是不好的，因为它会带来短期收益，但会带来长期痛苦。为什么？

点击一个 web 控制台，在相同的临时和实时环境中设置多个 webapps 和 API 将是重复和枯燥的。当压力增大时，不一致性就会出现。随着时间的推移，环境内部和环境之间都会有漂移。只有一个人进行点击，其他团队成员不会记录或重复使用。最终，在 web 控制台上手动设置环境将成为准确性和团队授权的克星。云原生环境可以由 API 驱动，并基于模板构建。那么为什么不自动化一切呢？

如果我们承认我们必须自动化所有的事情，我们应该在愿望清单上添加什么呢？怎么样:

*   当我们将配置更改推入 git repo 时，自动部署。我们相信每当这种情况发生时，彩虹就会出现在世界的某个地方，✨🌈🙌
*   使用模板隐藏样板文件，这样您就可以专注于部署到 Kubernetes 上的每个 webapp 或 API 的独特之处
*   使用具有幂等更新的声明式配置。别担心，我稍后会解释的
*   把所有东西都放入 git，包括加密秘密，这样我们就可以像处理拉请求的代码一样管理基础设施
*   从 git release webhook 事件自动创建一个发布构建映像，并对该映像应用相同的标记
*   跨应用程序使用一致的运行时版本，并轻松地对基础映像层定期应用安全补丁

这要求过分吗？当然不是！你可以使用让你在笔记本电脑上运行 Kubernetes 的 [Minishift](https://www.okd.io/minishift/) 来试驾 OCD。与其试图在一篇文章中涵盖所有这些要点，不如让我们看看 OCD wiki 的简短教程，其中[介绍了预构建映像的声明性部署](https://github.com/ocd-scm/ocd-meta/wiki/Short-Tutorial:-Git-Driven-Deployments-On-Minishift)。该演示涵盖了前四点。它让你在笔记本电脑上设置好上面视频中显示的东西💻✨🌈。下面是序列图:

[![sequence diagram](img/36db854ebcdbc9da6aab5a741d96fcac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EDoq-njl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1jdlc10gcp49xcht71wl.png)

左边是 git 将配置推入 git rep。我们使用 GitHub。您可以使用任何您喜欢的 git 服务器。该演示使用 Gitea 作为运行在 Minishift 中的 git 服务器，这样一切都可以在您的笔记本电脑上运行。webhook 触发一个名为 [ocd-environment-webhook](https://github.com/ocd-scm/ocd-environment-webhook/blob/master/README.md) 的应用。这是 awesome [adnanh/webhook](https://github.com/adnanh/webhook) 工具的一个实例，该工具被配置为运行脚本来从 git 获取配置，并使用 Helmfile 和 Tiller 将其安装到 Kubernetes 中。蒂勒会把我们的彩虹安装到库伯内特斯。我们将在以后的文章中介绍 Helmfile 和 Tiller。下面是演示安装的完整配置:

```
repositories:
  - name: ocd-meta 
    url: https://ocd-scm.github.io/ocd-meta/charts
releases:
  - name: {{ requiredEnv "ENV_PREFIX" }}-realworld
    labels: 
      deployer: {{ requiredEnv "ENV_PREFIX" }}-realworld
    chart: ocd-meta/ocd-deployer
    version: "1.0.0"
    values:
      - name: react-redux-realworld
      - replicas: 2
      - imageStreamTag: "react-redux-realworld:v0.0.1"
      - deploy_env: 
        - name: API_ROOT
          value: https://conduit.productionready.io/api 
```

这种配置是 Helmile yaml。主体是带有图表类型`ocd-deployer`的单个发布。图表是一组打包在 zip 文件中的 yaml 模板，可以从网站上下载。标题将 GitHub 上的 OCD 图表 repo 命名为下载图表的位置。应用于图表的模板值非常简单。它指定使用容器映像作为`react-redux-realworld:v0.0.1`，并且要维护两个副本容器。这是 realworld.io ReactJS 演示应用程序的预建图像。它还设置了一个环境变量`API_ROOT`作为应用程序将使用的公共 API。

要部署它，您需要一些先决条件:

1.  安装舵柄的迷你换档详细信息[此处](https://github.com/ocd-scm/ocd-meta/wiki/Helm-Tiller-on-Minishift)
2.  安装在 Minishift 上的 Gitea 详细信息[此处](https://github.com/ocd-scm/ocd-meta/wiki/Gitea-On-MiniShift)

一旦运行了 Gitea，您就可以注册一个用户，创建一个空的 repo `ocd-demo-env-short`，并将演示配置推送到其中:

```
# clone the code
git clone https://github.com/ocd-scm/ocd-demo-env-short.git
cd ocd-demo-env-short
# make sure we can load details about the gitea url
oc project gitea
# this should print the gitea url. If it doesn't set it manually
GITEA_URL=$(oc get routes | awk '$1~/gitea/{print $2}')
echo $GITEA_URL
# see instructions to setup your own person access token
ACCESS_TOKEN=f64960a3a63f5b6ac17916c9be2dad8dc76c7131
# set this to your username in gitea needed to get the url to your repo below 
USER_NAME=you_not_me
# add the gitea repo as a remote
git remote add minishift http://$ACCESS_TOKEN@$GITEA_URL/$USER_NAME/ocd-demo-env-short.git
# push the code into Gitea
git push minishift master 
```

我们为什么要这么做？因为我们无法在我的 repo 上设置 GitHub webhook 来将事件触发到您的部署管道中，该部署管道在您的笔记本电脑上的 Minishift 中运行。我们将代码加载到 Gitea repo 中，这样您就可以配置自己的 webhook。

接下来我们需要部署我们的`ocd-enviroment-webhook`处理程序，它将捕获 webhook 事件并部署我们的配置。我们可以使用一个脚本在它自己的项目中设置它:

```
oc logout ; oc login -u developer -p password
echo Use this git repo url http://$ACCESS_TOKEN@$GITEA_URL/$USER_NAME/ocd-demo-env-short.git
# this must match where tiller is installed
export TILLER_NAMESPACE=tiller-namespace
# create a new project
export PROJECT=ocd-short-demo
oc new-project $PROJECT
oc project $PROJECT
# upgrade to admin
oc logout ; oc login -u admin -p admin
oc project $PROJECT
pushd /tmp && oc project $PROJECT && curl -L https://github.com/ocd-scm/ocd-environment-webhook/archive/v1.0.1.tar.gz | tar zxf - \
&& cd ocd-environment-webhook-1.0.1 \
&& ./wizard.sh && popd 
```

注意，echos 显示出您需要输入到`wizard.sh`脚本中的 Git repo URL。下面是我运行的脚本，我大部分时间都是按回车键接受默认值:

```
The git repo url? http://6d42f3eb637f802cf0b2d17411ae2c2d26eefa54@gitea-gitea.192.168.99.100.nip.io/simbo1905/ocd-demo-env-short.git
The project where the images are built and promoted from? ocd-short-demo
Repo name? (default: simbo1905/ocd-demo-env-short): 
Branch ref? (default: refs/heads/master): 
Chart instance prefix? (default: ocd-short-demo): 
Use --insecure-no-tls-verify? (default: false): 
```

它在 Gitea 中设置了 webhook，你需要 webhook URL 和 webhook secret。这将输出 URL:

```
$ oc get route ocd-environment | awk 'NR>1{print "http://" $2 "/hooks/ocd-environment-webhook"}'
http://ocd-environment-ocd-short-demo.192.168.99.100.nip.io/hooks/ocd-environment-webhook 
```

而这个秘密:

```
$ oc describe dc ocd-environment-webhook | awk '$1~/WEBHOOK_SECRET:/{print $2}'
M7MuW6aZnn 
```

然后使用它们来建立一个类型为`application/json`的 Gitea webhook:

[![webhook setup](img/b84533c9cb2b5916860c06f152b029d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E00oOl79--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0gtnggups05f2vxk10ij.png)

现在，您可以在命令行上从 git 启动 webhook，或者使用 Gitea web 控制台编辑文件。要在命令行上完成，请尝试:

```
echo "testing" >> README.md
git commit -am 'test commit'
git push minishift master 
```

然后，您应该会看到 webhook 启动，我们的应用程序部署完毕💨✨🌈

之后，您可以按照上面视频中的步骤编辑容器映像版本号，以查看应用程序执行滚动升级。为了获得额外的分数，你可以尝试本教程末尾的步骤，回滚到第一个版本。

在那篇教程中，我们讨论了上面愿望清单中六个项目中的四个。在下一篇文章中，我们将从 git webhook release 事件中自动构建一个发布版本，并将相同的标签应用于图像。这样，我们可以准确地跟踪哪些代码在不同的环境之间被提升。同时，我们将使跨应用程序使用一致的运行时版本变得容易。这将使安全修补运行时映像变得容易。