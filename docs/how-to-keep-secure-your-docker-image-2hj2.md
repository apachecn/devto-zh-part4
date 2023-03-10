# 如何保护你的码头工人形象

> 原文：<https://dev.to/tomoyamachi/how-to-keep-secure-your-docker-image-2hj2>

# 概述

容器是近年来最好的工具之一。但是它还没有为集装箱的发展而建立。许多组织无法保持安全容器的生态系统。

这篇文章旨在什么是 Docker 图片的安全最佳实践。

你知道安全用的[克莱尔](https://github.com/coreos/clair)和[码头工人工作台](https://github.com/docker/docker-bench-security)吗？但这还不够。

# TL；分升

*   **CIS 基准发布 Docker 安全检查点**
*   **通过 Dockle 和 Trivy 检查您的图像**
*   **在 docker 推送**之前，在 CI 中轻松检查

# CIS 基准

互联网安全中心(CIS)提供的最佳安全实践之一。
他们的`Container Images and Build File`有 11 个检查站。

1.  为容器创建一个用户
2.  对容器使用可信基础映像
3.  不要在容器中安装不必要的包
4.  扫描并重建映像，以包含安全补丁
5.  为 Docker 启用内容信任
6.  向容器映像添加健康检查指令
7.  不要在 docker 文件中单独使用更新指令
8.  删除映像中的 setuid 和 setgid 权限
9.  在 Dockerfile 中使用复制而不是添加
10.  不要在 docker 文件中存储机密
11.  仅安装经过验证的软件包

克莱尔只支持`Scan and rebuild the images to include security patches`。

# 什么是`Dockle`

`Dockle`是`Simple Security Auditing and helping build the Best Docker Image`工具。

|  | 多克尔 | [码头工人安全工作台](https://github.com/docker/docker-bench-security) | 清楚 | [繁琐](https://github.com/knqyf263/trivy) |
| --- | --- | --- | --- | --- |
| 1.为容器创建一个用户 | 981 号房 | 981 号房 | - | - |
| 2.对容器使用可信基础映像 | - | - | - | - |
| 3.不要在容器中安装不必要的包 | - | - | - | - |
| 4.扫描并重建映像，以包含安全补丁 | - | - | 981 号房 | 981 号房 |
| 5.为 Docker 启用内容信任 | 981 号房 | 981 号房 | - | - |
| 6.向容器映像添加健康检查指令 | 981 号房 | 981 号房 | - | - |
| 7.不要在 docker 文件中单独使用更新指令 | 981 号房 | 981 号房 | - | - |
| 8.删除映像中的 setuid 和 setgid 权限 | 981 号房 | - | - | - |
| 9.在 Dockerfile 中使用复制而不是添加 | 981 号房 | 981 号房 | - | - |
| 10.不要在 docker 文件中存储机密 | 981 号房 | - | - | - |
| 11.仅安装经过验证的软件包 | - | - | - | - |

并且`Dockle`可以检测 [CVE-2019-5021](https://blog.aquasec.com/cve-2019-5021-alpine-docker-image-vulnerability) 等一些安全漏洞。

# 使用 Dockle

您可以轻松开始！

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

你可以在这里检查安装[。](https://github.com/goodwithtech/dockle#installation)

运行结果在这里。
[![passed](img/b74c5035458564e1737def7b72b8c03a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--arzGmXpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/goodwithtech/dockle/raw/master/imgs/usage_pass_light.png) 
[![failed](img/b74c5035458564e1737def7b72b8c03a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--arzGmXpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/goodwithtech/dockle/raw/master/imgs/usage_pass_light.png)

# 入住 CI

你可以用[特里维](https://github.com/knqyf263/trivy)代替[克莱尔](https://github.com/coreos/clair)。
Trivy 易于启动，支持库包和比 Clair 更好的准确性。

## 循环

```
jobs:
  build:
    docker:
      - image: docker:18.09-git
    steps:
      - checkout
      - setup_remote_docker
      - restore_cache:
          key: vulnerability-db
      - run:
          name: Build image
          command: docker build -t ci-test:${CIRCLE_SHA1} .
      - run:
          name: Install dockle
          command: |
            apk add --update curl
            VERSION=$(
                curl --silent "https://api.github.com/repos/goodwithtech/dockle/releases/latest" | \
                grep '"tag_name":' | \
                sed -E 's/.*"v([^"]+)".*/\1/'
            )

            wget https://github.com/goodwithtech/dockle/releases/download/v${VERSION}/dockle_${VERSION}_Linux-64bit.tar.gz
            tar zxvf dockle_${VERSION}_Linux-64bit.tar.gz
            mv dockle /usr/local/bin
      - run:
          name: Scan the local image with dockle
          command: dockle --exit-code 1 ci-test:${CIRCLE_SHA1}          

      - run:
          name: Install trivy
          command: |
            apk add --update curl
            VERSION=$(
                curl --silent "https://api.github.com/repos/knqyf263/trivy/releases/latest" | \
                grep '"tag_name":' | \
                sed -E 's/.*"v([^"]+)".*/\1/'
            )
            wget https://github.com/knqyf263/trivy/releases/download/v${VERSION}/trivy_${VERSION}_Linux-64bit.tar.gz
            tar zxvf trivy_${VERSION}_Linux-64bit.tar.gz
            mv trivy /usr/local/bin
      - run:
          name: Scan the local image with trivy
          command: trivy --exit-code 1 --quiet --auto-refresh trivy-ci-test:${CIRCLE_SHA1}
      - save_cache:
          key: vulnerability-db
          paths:
            - $HOME/.cache/trivy
workflows:
  version: 2
  release:
    jobs:
      - build 
```

## 特拉维斯

```
services:
  - docker

env:
  global:
    - COMMIT=${TRAVIS_COMMIT::8}

before_install:
  - docker build -t ci-test:${COMMIT} .
  - export DOCKLE_VERSION=$(curl --silent "https://api.github.com/repos/goodwithtech/dockle/releases/latest" | grep '"tag_name":' | sed -E 's/.*"v([^"]+)".*/\1/')
  - wget https://github.com/goodwithtech/dockle/releases/download/v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.tar.gz
  - tar zxvf dockle_${DOCKLE_VERSION}_Linux-64bit.tar.gz
  - export TRIVY_VERSION=$(curl --silent "https://api.github.com/repos/knqyf263/trivy/releases/latest" | grep '"tag_name":' | sed -E 's/.*"v([^"]+)".*/\1/')
  - wget https://github.com/knqyf263/trivy/releases/download/v${TRIVY_VERSION}/trivy_${TRIVY_VERSION}_Linux-64bit.tar.gz
  - tar zxvf trivy_${TRIVY_VERSION}_Linux-64bit.tar.gz
script:
  - ./dockle --exit-code 1 ci-test:${COMMIT}
  - ./trivy --exit-code 1 --quiet --auto-refresh ci-test:${COMMIT}
cache:
  directories:
    - $HOME/.cache/trivy 
```

# 结论

您可以检查您的 Docker 容器图像以使用`Dockle`和`Trivy`！
这些是 OSS 工具。

如果你觉得我错过了什么，弄错了一些细节，或者只是想说声嗨，请随时在下面留下评论，或者在 [GitHub](https://github.com/tomoyamachi) 或 [Twitter](https://twitter.com/tomoyamachi) 上联系我。