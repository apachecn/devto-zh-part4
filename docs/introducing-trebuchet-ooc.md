# 投石机简介

> 原文：<https://dev.to/nlowe/introducing-trebuchet-ooc>

我的团队的众多职责之一就是帮助我们的开发人员构建和发布他们的软件。我们努力做的事情是让新的开发者和团队尽可能容易地使用我们选择的平台。对于大多数团队来说，CI/CD 管道中的最后一步是将 docker 容器发布到某个注册中心。对我们来说，这恰好是[亚马逊 ECR](https://aws.amazon.com/ecr/) 。

为了简化大多数团队的工作，我们编写了一个名为`trebuchet`的新工具:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [海兰软件](https://github.com/HylandSoftware) / [投石机](https://github.com/HylandSoftware/trebuchet)

### 将容器图像放入 Amazon ECR

<article class="markdown-body entry-content container-lg" itemprop="text">

# 投石机——将容器图像发送到 Amazon ECR

[![Build Status](img/aac9cc0361da155416771c8e52b23ae0.png)](https://travis-ci.org/HylandSoftware/trebuchet)[![Coverage Status](img/d6772c932562013ed6e0f6de328ccd7a.png)](https://coveralls.io/github/HylandSoftware/trebuchet?branch=master)[![Go Report Card](img/12d8e45466c4e7e38a2d77d9f8f9016e.png)](https://goreportcard.com/report/github.com/hylandsoftware/trebuchet)

[![](img/b2af2a3025b8c9b3030a1071c05ceded.png)](https://raw.githubusercontent.com/HylandSoftware/trebuchet/master/logo/trebuchet_200x200.png)

* * *

Trebuchet 的目的是提高将 Docker 图片推送到亚马逊弹性容器注册表(ECR)的生活质量。

## 使用

`Trebuchet`以单个二进制文件(Linux/Windows)和 Docker 映像的形式提供。所有图片都可以在[这里](https://hub.docker.com/r/hylandsoftware/trebuchet)找到。

### 命令

`push`:

```
Pushes a Docker image into ECR
Region
        Region is required to be set as a flag, as an AWS environment variable (AWS_DEFAULT_REGION), or in the AWS config
Profile:
        Profile may be set as a flag or an AWS environment variable. 

Amazon Resource Name (ARN):
        Passing in a valid ARN allows trebuchet to assume a role to perform actions within AWS. A typical use-case for this
        would be a service account to use in a software pipeline to push images to ECR.

Aliases:
        trebuchet push can also be used as 'treb launch' or 'treb fling' for a more
```

…</article>

[View on GitHub](https://github.com/HylandSoftware/trebuchet)

为了理解我们为什么为此编写了一个工具，让我们看一下使用 CI 系统中的现有工具发布图像所涉及的所有内容。

## 古老的方式

在我们做任何事情之前，我们必须安装 AWS CLI 及其依赖项。我们使用了 Jenkins 和 Kubernetes 插件，所以这里有几个选项。我们可以将 CLI 放入构建 pod 使用的容器中，也可以在构建时安装它以保持构建容器的简单性。

### 安装 CLI

大多数管道中常见的 Jenkins 管道步骤如下所示:

```
stage('install-tools') {
    steps {
        container('docker') {
            withCredentials([file(credentialsId: 'aws-credentials', variable: 'AWS_CREDENTIALS')]) {
                sh """
                    mkdir -p \${HOME}/.aws
                    cp "${AWS_CREDENTIALS}" \${HOME}/.aws/credentials
                    cp ./ci/aws-config \${HOME}/.aws/config
                    apk update && apk add py-pip
                    pip install awscli --upgrade --user
                    PATH="\${PATH}:\${HOME}/.local/bin"
                    which aws
                    aws --version
                """
            }
        }
    }
} 
```

首先，我们从 Jenkins secret 复制我们的 CI 凭据。然后，我们安装`pip`，python 包管理器，因此我们可以安装 AWS CLI 包。我们还更新了`PATH`环境变量，以防 pip 填充路径不存在。

### 发布图片

现在我们已经安装了工具，我们准备好推送我们的 docker 映像了:

```
stage('publish') {
    steps {
        container('docker') {
            sh '''
                PATH="\${PATH}:\${HOME}/.local/bin"
                eval $(aws ecr get-login --no-include-email)
                REPO_NAME="$(aws ecr describe-repositories --repository-names ecr-demo --output text --query 'repositories[*].repositoryUri' || aws ecr create-repository --repository-name ecr-demo --output text --query 'repository.repositoryUri)"
                docker tag ecr-demo:${APP_VERSION} ${REPO_NAME}:${APP_VERSION}
                docker tag ecr-demo:${APP_VERSION} ${REPO_NAME}:latest
                docker push ${REPO_NAME}:${APP_VERSION}
                docker push ${REPO_NAME}:latest
                echo ${REPO_NAME} > repo_name.txt
            '''
        }
    }
} 
```

因为 Jenkins 为每个步骤调用重置了`PATH`变量，所以我们必须重新添加 pip shim 路径，这样我们才能找到 AWS CLI。然后，我们使用 CLI 为我们的用户获取`docker login`凭证。

ECR 要求我们在推送新图像之前创建一个`repository`。如果它不存在，我们可以通过向 AWS 请求 URI 来创建一个，如果我们返回一个错误，尝试创建一个。

最后，我们可以用完整的 ECR 报告 URI 重新标记我们的图像，并`docker push`它们。

## 使用投石机

在我们的 Jenkins build pod 中，我们没有使用 docker 守护进程，而是使用了`hylandsoftware/trebuchet`。比如:

```
spec:
  containers:
  - name: jnlp
    image: jenkins/jnlp-slave
  - name: trebuchet
    image: hylandsoftware/trebuchet
    tty: true
    securityContext:
      privileged: true 
```

这个映像运行一个`dind` docker 守护进程作为它的入口点，所以我们仍然可以像往常一样`docker build`我们的映像。但是，现在推到集控室的是一个 ***单个*** 命令:

```
stage('Push Docker Image to ECR') {
    steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws_credentials_id']) {
            container('trebuchet') {
                sh 'treb push ecr-demo:${APP_VERSION}'
            }
        }
    }
} 
```

如果需要，Trebuchet 会负责为我们创建 ECR 存储库。它还会根据需要重新标记图像，然后从本地 docker 守护进程中删除临时 ECR 标记。

## 总之

感谢我的同事[杰森](https://github.com/jhindulak)在这件事上带头冲锋陷阵。

投石机是在麻省理工学院许可下发布的；我们希望你会发现它有用。一定要打开一个 GitHub 问题，或者提交一个 Pull 请求，以获得您希望看到实现的任何 bug 或新功能！