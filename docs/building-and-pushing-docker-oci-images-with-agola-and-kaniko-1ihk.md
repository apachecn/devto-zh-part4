# 与 Agola 和 Kaniko 一起建立和推广 docker/OCI 的形象

> 原文：<https://dev.to/sgotti/building-and-pushing-docker-oci-images-with-agola-and-kaniko-1ihk>

如今，一个常见的需求是在您的 CI/CD 系统中构建和推送 docker/OCI 映像。用户在这样做时面临的一个主要问题是如何在容器中构建映像(Agola 默认使用容器来执行运行任务)。这通常需要使用 docker-in-docker 和 so 特权容器或绑定挂载主机 docker 套接字。这些选择可能会面临一些安全问题或打破容器隔离。

借助 Agola，您可以构建和推送 docker/OCI 映像，而无需借助像 [kaniko](https://github.com/GoogleContainerTools/kaniko) 这样的工具来打破容器隔离。

这里有一个示例库[这里](https://github.com/agola-io/agola-example-kaniko)包含一个简单的 go 程序，它将被构建并放在 docker/OCI 映像中。

### 建筑形象

让我们浏览一下本例中的[首次运行定义](https://github.com/agola-io/agola-example-kaniko/blob/master/.agola/config.yml)(本例中使用的 agola 配置格式是`yaml`格式。但是对于更高级的定义，你可以使用 [jsonnet](https://agola.io/doc/config/reference.html#config-file-formats) 格式:

```
version: v0

runs:
  - name: build image
    tasks:
      - name: build
        runtime:
          containers:
            - image: golang:1.12-stretch
        steps:
          - clone:
          - run:
              name: build the program
              command: go build .
          # Copy the built binary and the Dockerfile to the workspace
          - save_to_workspace:
              contents:
                - source_dir: .
                  dest_dir: /bin/
                  paths:
                    - agola-example-kaniko
                - source_dir: .
                  dest_dir: /
                  paths:
                    - Dockerfile
                    - .dockerignore
      - name: build docker image
        runtime:
          containers:
            - image: gcr.io/kaniko-project/executor:debug
        shell: /busybox/sh
        working_dir: /workspace
        steps:
          - restore_workspace:
              dest_dir: .
          - run: /kaniko/executor --no-push
        depends:
          - build 
```

这个运行定义将构建 go 程序，然后创建一个映像，而不推它。

这是由两个任务组成的运行，第一个任务将检查代码并构建 go 二进制文件。然后它会复制二进制文件和[运行工作区](https://agola.io/doc/concepts/workspaces.html)中的`Dockerfile`。任务将执行 kaniko 并构建图像。

`build docker image`任务使用官方的`gcr.io/kaniko-project/executor:debug`图像。使用带有 debug 标记的映像是因为它还提供了 busybox shell(用于调试目的)。我们必须覆盖默认的 shell，因为在这个映像中，busybox 二进制文件位于`/busybox/`目录中。Kaniko 默认期望`/workspace`中的“docker 上下文”,所以我们将使用`working_dir`任务选项来设置它。

#### 构建和推送图像

通常，构建映像而不将其推送到注册表是没有多大用处的，所以让我们来看看推映像所需的更改。

我们只需要给任务添加几行代码。

Kaniko 记录了一些向 gcr 和 aws 注册中心进行认证的方法，它的图像已经包含了 amazon ecr 的凭证助手。更多信息请参考 kaniko 文档。

对于 dockerhub 或您自己的注册表，您应该创建一个 docker `config.json`配置文件(在`/kaniko/.docker/config.json`中),其中包含所需的 auth 数据。

```
version: v0

runs:
  - name: build and push image
    tasks:
      - name: build
        [...]
      - name: build docker image
        runtime:
          containers:
            - image: gcr.io/kaniko-project/executor:debug
        environment:
          DOCKERREGISTRY_URL: "https://index.docker.io/v1/"
          DOCKERAUTH:
            from_variable: dockerauth
        shell: /busybox/sh
        working_dir: /workspace
        steps:
          - restore_workspace:
              dest_dir: .
          - run:
              name: generate docker config
              command: |
                cat << EOF > /kaniko/.docker/config.json
                {
                  "auths": {
                    "$DOCKERREGISTRY_URL": { "auth" : "$DOCKERAUTH" }
                  }
                }
                EOF
          # build and push with kaniko. You can add more than one --destination option.
          - run: /kaniko/executor --destination your/image:tag
        depends:
          - build 
```

环境变量`DOCKERAUTH`由 Agola projectgroup/project 中定义的变量填充，而对于本例来说，`DOCKERREGISTRY_URL`值是在运行定义中定义的。

值得注意的是，由于强大的 [Agola 秘密/变量系统](https://agola.io/doc/concepts/secrets_variables.html)，您能够基于不同的条件(分支、标签、拉请求)映射不同的变量，因此您可以重用相同的运行定义，并为您的开发分支、拉请求、主分支或标签使用不同的注册中心和授权