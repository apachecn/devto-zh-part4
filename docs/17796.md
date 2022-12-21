# 建立最佳实践 Docker 形象后你应该做的一件事(3 分钟)

> 原文：<https://dev.to/tomoyamachi/the-one-thing-you-should-do-after-building-best-practice-docker-image-in-3-minutes-3mi4>

# 概述

今天，许多公司尝试使用 Docker。这篇文章是写/写[最佳实践文档](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)的。

我创建了一个工具，通过 3 分钟内建立的图像分析你的 docker 文件。
[![](img/929344fbb7732252b29c6354f1a135d0.png)T3】](https://github.com/goodwithtech/dockle)

> [Dockle——用于安全的容器图像标记，帮助建立最佳实践 Docker 图像，易于开始](https://github.com/goodwithtech/dockle)

多克尔能够深入检查。(文件权限，凭据文件...)所以，Dockle 是检查重要规则的最佳工具，尤其是为了安全。

(我也是 [Vuls](https://github.com/future-architect/vuls) 和 [Trivy](https://github.com/knqyf263/trivy) 的主提交人。这些是著名的漏洞扫描器。)

这不是一个 Dockerfile Linter(像一个 hadolint)。

它也能够检查集装箱基础图像上的安全风险。Dockerfile Linter 从不这么做。

我希望你[开始它](https://github.com/goodwithtech/dockle)！

# 如何使用

## 家酿( [Mac](https://brew.sh/) / [Linux](https://docs.brew.sh/Homebrew-on-Linux) )

```
$ export DOCKER_CONTENT_TRUST=1
$ docker build -t test-image:v1 .
$ brew install goodwithtech/dockle/dockle
$ dockle test-image:v1 
```

## Linux

```
$ export DOCKER_CONTENT_TRUST=1
$ docker build -t test-image:v1 .
$ VERSION=$(
 curl --silent "https://api.github.com/repos/goodwithtech/dockle/releases/latest" | \
 grep '"tag_name":' | \
 sed -E 's/.*"v([^"]+)".*/\1/' \
) && curl -L -o dockle.tar.gz https://github.com/goodwithtech/dockle/releases/download/v${VERSION}/dockle_${VERSION}_Linux-64bit.tar.gz
$ tar zxvf dockle.tar.gz
$ ./dockle test-image:v1 
```

## 窗口

```
$ export DOCKER_CONTENT_TRUST=1
$ docker build -t test-image:v1 .
$ VERSION=$(
 curl --silent "https://api.github.com/repos/goodwithtech/dockle/releases/latest" | \
 grep '"tag_name":' | \
 sed -E 's/.*"v([^"]+)".*/\1/' \
) && curl -L -o dockle.zip https://github.com/goodwithtech/dockle/releases/download/v${VERSION}/dockle_${VERSION}_Windows-64bit.zip
$ unzip dockle.zip && rm dockle.zip
$ ./dockle.exe test-image:v1 
```

## 通过 Docker

```
$ VERSION=$(
 curl --silent "https://api.github.com/repos/goodwithtech/dockle/releases/latest" | \
 grep '"tag_name":' | \
 sed -E 's/.*"v([^"]+)".*/\1/' \
) && docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
 goodwithtech/dockle:v${VERSION} test-image:v1 
```

当您想在您的主机上扫描映像时，您只需要-v/var/run/docker . sock:/var/run/docker . sock。

# 结果

```
PASS    - CIS-DI-0001: Create a user for the container
PASS    - CIS-DI-0005: Enable Content trust for Docker
PASS    - CIS-DI-0006: Add HEALTHCHECK instruction to the container image
PASS    - CIS-DI-0007: Do not use update instructions alone in the Dockerfile
PASS    - CIS-DI-0008: Remove setuid and setgid permissions in the images
PASS    - CIS-DI-0009: Use COPY instead of ADD in Dockerfile
PASS    - CIS-DI-0010: Do not store secrets in ENVIRONMENT variables
PASS    - CIS-DI-0010: Do not store secret files
PASS    - DKL-DI-0001: Avoid sudo command
PASS    - DKL-DI-0002: Avoid sensitive directory mounting
PASS    - DKL-DI-0003: Avoid apt-get/apk/dist-upgrade
PASS    - DKL-DI-0004: Use apk add with --no-cache
PASS    - DKL-DI-0005: Clear apt-get caches
PASS    - DKL-DI-0006: Avoid latest tag
PASS    - DKL-LI-0001: Avoid empty password
PASS    - DKL-LI-0002: Be unique UID
PASS    - DKL-LI-0002: Be unique GROUP 
```

该工具检查 [CIS 基准](https://www.cisecurity.org/benchmark/docker/)和[编写 Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) 的最佳实践。

当出现问题时，Dockle 会显示改进建议的短信。

```
WARN    - CIS-DI-0001: Create a user for the container
        * Last user should not be root
PASS    - CIS-DI-0005: Enable Content trust for Docker
WARN    - CIS-DI-0006: Add HEALTHCHECK instruction to the container image
        * not found HEALTHCHECK statement
PASS    - CIS-DI-0007: Do not use update instructions alone in the Dockerfile
INFO    - CIS-DI-0008: Remove setuid and setgid permissions in the images
        * Found setuid file: usr/lib/openssh/ssh-keysign urwxr-xr-x
FATAL   - CIS-DI-0009: Use COPY instead of ADD in Dockerfile
        * Use COPY : /bin/sh -c #(nop) ADD file:81c0a803075715d1a6b4f75a29f8a01b21cc170cfc1bff6702317d1be2fe71a3 in /app/credentials.json
FATAL   - CIS-DI-0010: Do not store secrets in ENVIRONMENT variables
        * Suspicious ENV key found : MYSQL_PASSWD
FATAL   - CIS-DI-0010: Do not store secret files
        * Suspicious filename found : app/credentials.json
PASS    - DKL-DI-0001: Avoid sudo command
FATAL   - DKL-DI-0002: Avoid sensitive directory mounting
        * Avoid mounting sensitive dirs : /usr
PASS    - DKL-DI-0003: Avoid apt-get/apk/dist-upgrade
PASS    - DKL-DI-0004: Use apk add with --no-cache
FATAL   - DKL-DI-0005: Clear apt-get caches
        * Use 'rm -rf /var/lib/apt/lists' after 'apt-get install' : /bin/sh -c apt-get update && apt-get install -y git
PASS    - DKL-DI-0006: Avoid latest tag
FATAL   - DKL-LI-0001: Avoid empty password
        * No password user found! username : nopasswd
PASS    - DKL-LI-0002: Be unique UID
PASS    - DKL-LI-0002: Be unique GROUP 
```

您可以查看搜索 analysys 代码的详细信息(`CIS-DI-0001`...)上[自述](https://github.com/goodwithtech/dockle#checkpoint-detail)。

你不介意你的图像没有通过检查点。这是一个指标。

有时候你必须以`root`的身份奔跑。

有时候 CLI 工具不需要`HEALTHCHECK`。

有时当你想添加 tar 文件时使用`ADD`语句。

我希望这是提醒你真的想这样做。

您可以指定忽略规则来给出`--ignore, -i`选项或创建`.dockleignore`。

```
$ dockle -i CIS-DI-0001 -i CIS-DI-0006 [IMAGE_NAME] 
```

```
# run as root
CIS-DI-0001
# don't use HEALTHCHECK
CIS-DI-0006
# use latest tag
DKL-DI-0006 
```

让我们再做一次。

```
IGNORE  - CIS-DI-0001: Create a user for the container
INFO    - CIS-DI-0005: Enable Content trust for Docker
        * export DOCKER_CONTENT_TRUST=1 before docker pull/build
IGNORE  - CIS-DI-0006: Add HEALTHCHECK instruction to the container image
PASS    - CIS-DI-0007: Do not use update instructions alone in the Dockerfile
INFO    - CIS-DI-0008: Remove setuid and setgid permissions in the images
        * Found setuid file: usr/lib/openssh/ssh-keysign urwxr-xr-x
IGNORE  - CIS-DI-0009: Use COPY instead of ADD in Dockerfile
FATAL   - CIS-DI-0010: Do not store secrets in ENVIRONMENT variables
        * Suspicious ENV key found : MYSQL_PASSWD
FATAL   - CIS-DI-0010: Do not store secret files
        * Suspicious filename found : app/credentials.json
PASS    - DKL-DI-0001: Avoid sudo command
FATAL   - DKL-DI-0002: Avoid sensitive directory mounting
        * Avoid mounting sensitive dirs : /usr
PASS    - DKL-DI-0003: Avoid apt-get/apk/dist-upgrade
PASS    - DKL-DI-0004: Use apk add with --no-cache
FATAL   - DKL-DI-0005: Clear apt-get caches
        * Use 'rm -rf /var/lib/apt/lists' after 'apt-get install' : /bin/sh -c apt-get update && apt-get install -y git
PASS    - DKL-DI-0006: Avoid latest tag
FATAL   - DKL-LI-0001: Avoid empty password
        * No password user found! username : nopasswd
PASS    - DKL-LI-0002: Be unique UID
PASS    - DKL-LI-0002: Be unique GROUP 
```

# 关闭

Dockle 不是 Dockerfile Linter，而是 Docker Image Linter。

多克尔可以深入调查。(文件权限，凭据文件...)所以，Dockle 能够比其他人检查重要的规则，尤其是安全性。

Dockle 也能够分析 stdin 创建的图像。你可以在很多地方使用它。

```
docker build -<<EOF FROM busybox
RUN echo "hello world" EOF 
```

希望你[喜欢](https://github.com/goodwithtech/dockle)！

谢谢！！