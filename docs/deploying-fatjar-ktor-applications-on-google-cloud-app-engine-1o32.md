# 在 Google 云应用引擎上部署 fatjar Ktor 应用程序

> 原文：<https://dev.to/viniciusccarvalho/deploying-fatjar-ktor-applications-on-google-cloud-app-engine-1o32>

# 原来的无服务器平台

谷歌应用引擎或简称 GAE。一直是运行您的 web 应用程序的神奇服务。一些亮点:

*   支持多种运行时( [java](https://cloud.google.com/appengine/docs/standard/java/) 、 [python](https://cloud.google.com/appengine/docs/python/) 、 [go](https://cloud.google.com/appengine/docs/standard/go111/) 、 [php](https://cloud.google.com/appengine/docs/standard/php7/) 、 [ruby](https://cloud.google.com/appengine/docs/standard/ruby/) 、 [nodejs](https://cloud.google.com/appengine/docs/standard/nodejs/) )
*   无服务器执行模式，只为运行中的请求付费
*   为某些服务提供免费层
*   支持版本控制和流量分割
*   后台任务的任务调度和排队
*   更多信息，请访问官方文档。

然而，对于 java 开发人员来说，一直存在一个问题，您只能部署 war 文件，这将您限制在 Servlet 规范中。

不再是了，从 java11 运行时开始，GAE 现在提供了一种运行自包含 jar 文件的方式( [Ktor](https://ktor.io) ，SpringBoot， [Micronaut](https://micronaut.io) ， [Quarkus](https://quarkus.io) )。

# Java 11 运行时简介

从 [java11](https://cloud.google.com/appengine/docs/standard/java11/) 运行时开始，您现在可以部署一个“fat jar”文件并执行它，而不是将 war 文件部署到 Jetty 容器中。

您需要做的就是创建一个新的`app.yaml`文件，其中包含正确的入口点命令:

```
runtime: java11
instance_class: F2
entrypoint: 'java  -jar  frontend-1.0-SNAPSHOT-all.jar'
service: default 
```

您可以将任何有效的 java 参数传递给 JRE，如果您想了解 yaml 文件的所有可能选项，请查看[文档](https://cloud.google.com/appengine/docs/standard/java11/config/appref)

# GAE ktor 的斯凯尔顿项目

今天我将分享我在 GAE 上运行 ktor 应用程序的 skelleton 项目。请随意从这里的[克隆它，并以它为起点。](https://github.com/viniciusccarvalho/ktor-on-gae-sample)

## 项目布局

```
.
├── build.gradle
├── gradle
│   └── wrapper
├── gradle.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
    ├── main
    │   ├── appengine
    │   ├── java
    │   ├── kotlin
    │   └── resources
    └── test
        ├── java
        ├── kotlin
        └── resources 
```

我一直喜欢标准的 gradle 项目布局。这里唯一的区别是我们的`main`目录下的`appengine`文件夹。这包含了`app.yaml`部署文件。

## 梯度配置

首先，这个项目使用的是 gradle 5.6。你的`gradle/wrapper/gradle-wrapper.properties`应该是这样的:

```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-5.6-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists 
```

`build.gradle`是足够通用的，你可以直接复制粘贴并在你的项目中使用它。正如我之前提到的，这是我在所有项目中使用的模板。

```
buildscript {
    ext.kotlin_version = '1.3.50'
    ext.ktor_version = '1.2.4'
    repositories {
        mavenCentral()
        jcenter()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        classpath 'com.github.jengelman.gradle.plugins:shadow:5.1.0'
        classpath 'com.google.cloud.tools:appengine-gradle-plugin:1.+'
    }
}

apply plugin: 'kotlin'
apply plugin: 'application'
apply plugin: 'com.github.johnrengelman.shadow'
apply plugin: 'com.google.cloud.tools.appengine'

group 'io.igx.cloud'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

mainClassName = "io.ktor.server.netty.EngineMain"

repositories {
    mavenCentral()
    jcenter()
}

appengine {
    stage {
        artifact = "build/libs/$project.name-$project.version-all.jar"
    }
}

test {
    useJUnitPlatform()
}

dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk8:$kotlin_version"
    implementation "io.ktor:ktor-server-core:$ktor_version"
    implementation "io.ktor:ktor-server-netty:$ktor_version"
    implementation "io.ktor:ktor-gson:$ktor_version"

    testImplementation "org.assertj:assertj-core:3.13.2"
    testImplementation "io.mockk:mockk:1.9.3"
    testImplementation "org.junit.jupiter:junit-jupiter:5.5.1"
    testImplementation 'org.koin:koin-test:2.0.1'

}

compileKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
compileTestKotlin {
    kotlinOptions.jvmTarget = "1.8"
} 
```

我正在使用[阴影](https://imperceptiblethoughts.com/shadow/)插件来创建我的胖罐子。它很好地集成到我的`application` gradle 阶段，并生成一个工件，在其名称后面加上`-all`。

我还修改了默认的`appengine`来指向通过`shadow`插件创建的新 jar。

最后，我添加了 gradle appengine 插件。

非常简单，但可重复使用，节省了我大量的时间。构建/部署

```
./gradlew clean build
./gradlew appengineDeploy 
```

就这些了，希望你喜欢。

快乐编码