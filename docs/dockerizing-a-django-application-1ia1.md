# 将 Django 应用程序归档

> 原文：<https://dev.to/luvpreet33/dockerizing-a-django-application-1ia1>

我已经和 Django 一起工作超过 1 年半了。但是最近我离开了 Django，一直忙于将 kubernetes 引入我们的基础设施。在引入 kubernetes 之前，您需要为您的应用程序准备一个 docker 图像。所以在这篇文章中，我将描述我在为 Django 应用程序构建一个像样的 docker 映像时所面临的挑战。我发表这篇文章的一个主要原因是，我可以从你们那里得到反馈，知道我还做错了什么。这里是[完整的 Dockerfile 文件](https://gist.github.com/luvpreetsingh/1a230bf2e4f36a2a1480f0ced15f0fc9)

## 保持图像尺寸尽可能小

我必须决定构建应用程序的基础映像。就像对 docker 一无所知一样，我选择了`python:3.6`并在它的基础上构建了我的应用程序。结果是一个 1.1GB 的图像。因此，经过一些阅读，在我选择`python:3.6-alpine`作为我的基本图像后，我开始了解`alpine` ❤️
，结果图像是 227MB。伙计们，这是一个巨大的差异。所以，永远用阿尔卑斯。小图片很容易推拉，节省你的时间和资源以及[我在这里不解释的其他原因](https://semaphoreci.com/blog/2018/03/14/docker-image-size.html)。

## 构建映像时安装私有 pip 包

我们在 python 应用程序中使用了一个 git repos 包。python 包管理器 **pip** 确实提供了一个从版本控制系统直接安装包的选项，但是挑战在于这个 repo 是私有的，并且在 docker 容器中安装一个私有 git 包是有挑战性的。您不希望在图像中留下您的凭据。

我去社区了解如何去做，人们正在做多阶段构建，在第一阶段传递他们的 ssh-key，在那里安装东西，然后把它们复制到第二阶段。我试过这种方法，但效果不太好。所以，我尝试了另一种方法。

我在 Github 中创建了一个 OAuth 令牌，并授予它对所需存储库的只读访问权。然后我创建了`docker-requirements.txt`文件，这个文件与原来的`requirements.txt`文件相同，但是只有一处改变。我将私有依赖项更改为:

```
-e git+https://${GIT_ACCESS_TOKEN}:x-oauth-basic@github.com/my-org/my-pckg.git@my.version#egg=my-egg 
```

它只是从环境中获取`GIT_ACCESS_TOKEN`变量。你可以在这里阅读更多关于[皮普和 VCS 的信息。](https://pip.pypa.io/en/stable/reference/pip_install/#vcs-support)

接下来的事情是在构建期间传递`GIT_ACCESS_TOKEN`,并且小心不要把它留在映像中。所以，我利用了[码头工人的秘密](https://docs.docker.com/develop/develop-images/build_enhancements/)。这是安装需求的步骤，

```
RUN --mount=type=secret,id=git_token,dst=/git_token export GIT_ACCESS_TOKEN=$(cat /git_token) \
&& apk add git \
&& pip install -r ../requirements/docker-requirements.txt \
&& apk del git 
```

我只是根据这个秘密创建了一个环境变量，然后安装了 git(因为需要它来克隆这个项目)。之后，我安装了需求，然后删除了 git 包。

## 构建映像时构建 pip 包

以上并不是我在 pip 软件包上遇到的唯一麻烦。因此，在安装了我的私有包之后，docker 构建仍然失败，因为 python 包仍然没有被安装，给出了错误`Failed building wheel for blah-blah-blah`。不仅仅是我们的私有包，还有其他模块如`gevent`、`psycopg2-binary`等。也失败了。当然，它会失败，因为我们使用的 alpine 没有安装任何构建依赖项。因此，我们必须安装构建依赖项以及能够使 Django 应用程序顺利运行的依赖项。

我不得不修改上面的步骤，

```
RUN --mount=type=secret,id=git_token,dst=/vogo_git_token export GIT_ACCESS_TOKEN=$(cat /vogo_git_token) \
    && apk add --no-cache --virtual .build-deps \
    ca-certificates gcc postgresql-dev linux-headers musl-dev \
    libffi-dev jpeg-dev zlib-dev git \
    && pip install -r ../requirements/docker-requirements.txt \
    && find /usr/local \
        \( -type d -a -name test -o -name tests \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    && runDeps="$( \
        scanelf --needed --nobanner --recursive /usr/local \
                | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                | sort -u \
                | xargs -r apk info --installed \
                | sort -u \
    )" \
    && apk add --virtual .rundeps $runDeps \
    && apk del .build-deps \
    && rm -rf src/my-pckg/.git 
```

我们首先安装构建依赖项，然后安装 pip 包。在 pip 包成功安装后，我们移除构建依赖项，因为我们希望保持我们的映像最小。它还从`/usr/local`位置删除冗余文件。然后，我们将安装运行依赖项，这有助于应用程序的平稳运行。为了识别运行依赖关系，它正在使用`scanelf`命令扫描在`/usr/local`目录中运行 python 包所需的可执行文件。[如果你想了解更多关于`scanelf`的信息，请点击这里](https://wiki.gentoo.org/wiki/Hardened/PaX_Utilities)。

最后，我将从克隆我的私有包的位置移除`.git`,因为如果有人在那个位置移除了`git config --list`,仍然可以检索到我的令牌。现在我把它去掉了，它就永远消失了。

非常诚实地说，我在这里发现了这个很酷的东西，完全归功于分享它的人。

## 保持层数最少

docker 图像的大小也取决于层数。您可以在上面看到，我在一个步骤中运行了许多命令。所有这些命令都是相关的，所以把它们放在一起没有坏处。我看过一些 docker 文件，

```
RUN mkdir /var/log/app-name/ 
```

更好的方法可能是在您的项目中创建一个 shell 脚本，

```
#!/bin/sh

echo making log directory
mkdir -p /var/log/consumer_api/

echo Running Server...
gunicorn -c gunicorn.conf api.wsgi 
```

然后在创建容器时让它运行

```
ENTRYPOINT ["/bin/sh", "start.sh"] 
```

您可以在这里轻松保存一个图层。

如果你已经到了这一步，感谢你阅读这篇文章。这些是我在这个过程中的发现，我很高兴与大家分享这些。在制作我的最终 docker 形象之前，我做了很多愚蠢的事情，你永远不知道你是否还在用愚蠢的方式做这件事，所以建议是最受欢迎的。