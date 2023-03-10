# Spring Boot 小于 80MB

> 原文：<https://dev.to/gofabian/dockerize-spring-boot-80mb-58m9>

我想知道通过一个简单的 Spring Boot 应用程序，我能减少多少 Docker 图片的大小。

# 高山尝试

因此，让我们使用[基础映像](https://hub.docker.com/_/alpine)和 [Alpine Linux](https://alpinelinux.org/) 。

Dockerfile 文件:

```
# use Alpine Linux for build stage
FROM alpine:3.10.1 as build

# install build dependencies
RUN apk --no-cache add openjdk11
RUN apk --no-cache add maven
... 
```

这将从 Dockerhub 获取轻量级 Alpine Linux 映像(~6MB)，并安装 OpenJDK 11 和 Maven 作为构建依赖项。之后，我们可以构建 Spring Boot 应用程序:

```
...
# fetch maven dependencies
WORKDIR /build
COPY pom.xml pom.xml
RUN mvn dependency:go-offline

# build
COPY src src
RUN mvn clean package
... 
```

如您所见，我们分两步完成。首先，我们下载所有的依赖项，然后编译代码。如果我们在 pom.xml 文件中没有更改的情况下再次构建图像，结果将从缓存中取出。这将节省一些时间，因为代码的变化比依赖关系更频繁。

最后，我们创建第二个 Docker 映像，它只包含执行所需的部分。Docker 将这一特性称为“多阶段”,其中第一个阶段是初步构建步骤，最后一个阶段产生执行映像:

```
...
# prepare a fresh Alpine Linux with JDK
FROM alpine:3.10.1
RUN apk --no-cache add openjdk11

# get result from build stage
COPY --from=build /build/target/*.jar /app.jar
VOLUME /tmp
EXPOSE 8080
CMD /usr/lib/jvm/default-jvm/bin/java -jar /app.jar 
```

我们使用`--from=build`参数来复制第一阶段的构建结果。所有其他部分都将被删除:Maven、Java 字节码、依赖项，...

结果有多大？

```
$ docker build -t lazy . && docker image ls | grep lazy
...
lazy    latest    5f63318a3a0b    11 seconds ago    288MB 
```

# Jlink 尝试

我觉得 288MB 太大了。我记得在 Java 9 中，标准库被分成模块。有一个 jlink 工具可以只用必要的模块构建一个 JDK。我们在`mvn`调用之前添加 jlink 执行:

```
...
# build JDK with less modules
RUN /usr/lib/jvm/default-jvm/bin/jlink \
    --compress=2 \
    --module-path /usr/lib/jvm/default-jvm/jmods \
    --add-modules java.base,java.logging,java.xml,jdk.unsupported, \
        java.sql,java.naming,java.desktop,java.management, \
        java.security.jgss,java.instrument \
    --output /jdk-minimal

# fetch maven dependencies
... 
```

此外，我们必须将最小 JDK 复制到执行阶段:

```
# prepare a fresh Alpine Linux with JDK
FROM alpine:3.10.1

# get result from build stage
COPY --from=build /jdk-minimal /opt/jdk/
COPY --from=build /build/target/*.jar /app.jar
VOLUME /tmp
EXPOSE 8080
CMD /opt/jdk/bin/java -jar /app.jar 
```

那会缩小尺寸吗？

```
$ docker build -t lazy . && docker image ls | grep lazy

...

lazy    latest    fbdc64bb6826    20 seconds ago    79.5MB 
```

# 
  
汇总

我们删除了大约 200 兆不必要的 JDK 模块。我喜欢这样。如果我们使用 GraalVM 的[原生映像特性，可能尺寸会更小？](https://www.graalvm.org/docs/reference-manual/aot-compilation/)

结果:79.5MB

*   16MB JAR 文件
*   55MB open JDK 11“jlink”
*   8.5MB 高山 Linux +坞站映像

源代码:[https://gist . github . com/go Fabian/8 a0f 951 BC 1 EDC 88 b 918 ce 1145 ccfbb 03](https://gist.github.com/gofabian/8a0f951bc1edc88b918ce1145ccfbb03)