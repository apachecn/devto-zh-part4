# 将幸运应用部署到弹性豆茎

> 原文：<https://dev.to/jwoertink/deploying-lucky-apps-to-elastic-beanstalk-2p0d>

> 这不是最漂亮的解决方案，但对我们来说很有效。如果你对如何清理这件事有什么想法，我完全赞成。

## 设置

*   在 macOS 上本地开发
*   生产使用[弹性豆茎](https://aws.amazon.com/elasticbeanstalk/)容器
*   EB 容器使用 Docker

## 概念

我们在让 Crystal 的交叉编译正常工作时遇到了问题，所以我们必须使用一些额外的步骤。

*   在本地启动 Docker 以编译应用程序的发布版本
*   将发布二进制文件和所需文件移动到临时目录
*   按照 EB 部署说明将临时目录压缩到`app.zip`
*   在本地使用`eb`命令将`app.zip`推送到 EB 容器

> 如果我们碰巧可以进行交叉编译，我们可以去掉这个过程中的前两步。

## 代码

我们需要创建一些文件。我将首先定义它们，然后展示代码。

*   `./script/deploy`。请务必`chmod +x ./script/deploy`
*   `./docker/BuildDockerfile`
*   `./docker/docker_run_build.sh`
*   `./docker/Dockerfile`
*   `./docker/Dockerrun.aws.json`

### 部署脚本

用于将代码推向生产。

```
#!/bin/bash

set -e

# don't execute next commands on error
trap 'exit' ERR

# let echo interpret escape chars (\n)
shopt -s xpg_echo

# 
START=`date +%s`
DATE=`date '+%Y%m%d@%H%M%S'`
BUILD_DIR=build-temp-${DATE}
SHA1=`git rev-parse HEAD`
RANDOM=`awk -v min=5 -v max=1000000 'BEGIN{srand(); print int(min+rand()*(max-min+1))}'`

# deploy to production when on master branch
# deploy to staging when on other branches
branch=$(git branch | sed -n -e 's/^\* \(.*\)/\1/p')
if [ "$branch" == "master" ]
then STAGE=production
else STAGE=staging
fi LABEL=$STAGE-$SHA1-$RANDOM-$DATE

# Remove the old build
rm -rf build/app

# Build assets
echo "Building Assets"
rm -rf public/assets/* public/mix-manifest.json
yarn prod

# Build the application
echo "Starting Docker"
docker build -t lucky-app-build -f docker/BuildDockerfile .
docker run -v $(pwd)/build:/app/build lucky-app-build

# Create a temporary directory to stage the files
mkdir ${BUILD_DIR}

# Stage the files
cp .env.${STAGE} ${BUILD_DIR}/.env
cp build/app ${BUILD_DIR}
cp -r public ${BUILD_DIR}
cp docker/Dockerfile ${BUILD_DIR}
cp docker/Dockerrun.aws.json ${BUILD_DIR}
cp -r .ebextensions ${BUILD_DIR}

# Getting them zipped up
cd ${BUILD_DIR}; zip -Xr app.zip * .env .ebextensions; mv app.zip ../; cd ..

# Deploy the application
eb deploy --label $LABEL

# Cleanup
rm -rf ${BUILD_DIR} app.zip

END=`date +%s`

echo Deploy ended with success! Time elapsed: $((END-START)) seconds 
```

Enter fullscreen mode Exit fullscreen mode

### 建筑坞型

用于构建发布二进制文件

```
FROM crystallang/crystal:0.29.0

ADD . /app
ADD ./docker/docker_run_build.sh /app/docker_run_build.sh

WORKDIR /app

RUN shards update && \
    rm -rf /app/build/app && \
    crystal build src/start_server.cr --release -o app

RUN chmod +x docker_run_build.sh

CMD ["./docker_run_build.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

### docker_run_build

我真的不知道为什么我们会有这个，但它在这里，所以它在这里。

```
#!/bin/sh

cp app /app/build/ 
```

Enter fullscreen mode Exit fullscreen mode

### Dockerfile

EB 上生产中使用的实际 docker 文件

```
FROM phusion/baseimage

ENV APP_DOMAIN=localhost
ENV SECRET_KEY_BASE=abc123abc123
ENV LUCKY_ENV=production
ENV PORT=8000
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get -q update && \
    apt-get -qy install build-essential libgc-dev libssl-dev libxml2-dev libyaml-dev libevent-dev && \
    apt-get -y install tzdata && \
    apt-get -y autoremove && \
    apt-get -y clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /tmp/*

RUN mkdir /app

ADD ./app /app
ADD ./.env /app/.env
ADD ./public /app/public

WORKDIR /app

EXPOSE 8000

CMD trap exit TERM; ./app & wait 
```

Enter fullscreen mode Exit fullscreen mode

### Dockerrun

该文件由 EB 使用。了解有关 AWS EB 的[单个集装箱码头](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/single-container-docker-configuration.html)的更多信息。

```
{  "AWSEBDockerrunVersion":  "1",  "Logging":  "/var/log/app.log",  "Ports":  [  {  "ContainerPort":  "8000"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 期末笔记

你需要确保用类似于
的东西来更新你的`.gitignore`文件

```
/build*
*.zip
.elasticbeanstalk/*
!.elasticbeanstalk/*.cfg.yml
!.elasticbeanstalk/*.global.yml 
```

Enter fullscreen mode Exit fullscreen mode

最后，这一切都假设你有你的弹性豆茎设置。这需要在本地安装`eb`二进制文件，设置容器，并添加所有配置文件。