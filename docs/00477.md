# windows 10 docker 快速入门:第 1 部分

> 原文：<https://dev.to/jeetu93/basic-docker-commands-for-windows-10-docker-app-quick-start-47ec>

*   首先从谷歌在你的本地 windows 10 机器上安装 docker 桌面应用
*   打开 docker 应用程序// docker 启动将显示在通知中
*   在 docker 运行之后

*   然后打开 cmd

# 显示 docker 安装版本

```
docker --version 
```

拉动 docker 图像

```
docker pull IMAGE_NAME:tag
i.e

docker pull ubuntu:latest 
```

# 列出可用的 docker 图片

```
docker image ls 
```

# 运行 docker 运行图像

```
docker run IMAGE_NAME 
```

# 现在列出哪个容器映射到您的 docker 映像并运行一个

```
docker ps 
```

# 运行你的图像的容器

```
docker exec -it CONTAINER_NAME /bin/bash 
```

！！现在，您处于容器的 bash 模式

# 提交容器中所做的更改

```
docker commit CONTAINER_NAME IMAGE_NAME:tag 
```

# 之后你可以停止容器，你的修改会被保存

```
docker stop CONTAINER_NAME 
```

# 列出所有活动容器

```
docker container ls 
```

# 列出所有活动图像/容器历史

```
docker image ls -a
docker container ls -a 
```