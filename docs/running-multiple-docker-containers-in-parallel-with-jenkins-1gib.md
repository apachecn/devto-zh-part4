# 与 Jenkins 并行运行多个 Docker 容器

> 原文：<https://dev.to/kabisasoftware/running-multiple-docker-containers-in-parallel-with-jenkins-1gib>

今天早上，我在寻找一种与 Jenkins 并行运行多个 Docker 容器的方法。尽管这对我来说似乎是一个常见的用例，但我还是花了一段时间才找到我需要的所有信息并把它们拼凑起来。如你所知，你唯一需要的设计模式是复制粘贴。
我写这篇文章是为了让你和我未来的自己从一个 Jenkinsfile 文件中复制粘贴一些有用的片段。

假设您有一个需要 PostgreSQL 数据库的 Java 应用程序。如果你想对那个应用程序进行一些集成测试，你可能也需要这个数据库是可访问的。
要让 Jenkins 为您运行这些集成测试，您只需在运行 Jenkins 的机器上安装 PostgreSQL，创建必要的数据库和用户，然后就可以了。
然而，我宁愿把每个应用程序放在自己的 Docker 容器中，只在需要的时候运行。

考虑下面的 Jenkinsfile:

```
def withDockerNetwork(Closure inner) {
  try {
    networkId = UUID.randomUUID().toString()
    sh "docker network create ${networkId}"
    inner.call(networkId)
  } finally {
    sh "docker network rm ${networkId}"
  }
}

pipeline {
  agent none

  stages {
    stage("test") {
      agent any

      steps {
        script {
          def database = docker.build("database", "database")
          def app = docker.build("app", "-f dockerfiles/ci/Dockerfile .")

          withDockerNetwork{ n ->
            database.withRun("--network ${n} --name database") { c ->
              app.inside("""
                --network ${n}
                -e 'SPRING_DATASOURCE_URL=jdbc:postgresql://database:5432/test'
              """) {
                sh "mvn verify"
              }
            }
          }
        }
      }
    }
  }
} 
```

函数`withDockerNetwork`(从[瑞安·德斯蒙](https://issues.jenkins-ci.org/browse/JENKINS-49567)复制粘贴而来)创建并最终删除一个随机命名的 Docker 网络。
创建网络后，它调用您选择的代码块，并为其提供这个随机名称。
代码块完成后，网络被删除。

语句`docker.build("database", "database")`使用上下文`database`构建一个名为“database”的 Docker 映像。
语句`docker.build("app", "-f dockerfiles/ci/Dockerfile .")`利用上下文`.`从 Docker 文件`dockerfiles/ci/Dockerfile`中构建一个名为“app”的 Docker 映像。

一旦构建了两个映像，基于这些映像的容器就会启动并连接到同一个网络，从而允许它们进行通信。
参数`--network ${n}`用于将两个容器连接到网络。
数据库的容器通过参数`--name database`被明确命名，这样我们就可以让应用程序指向它。
后者是通过用参数`-e 'SPRING_DATASOURCE_URL=jdbc:postgresql://database:5432/test'`设置一个环境变量来实现的。这最后一步是 Spring 特有的。对于您自己的用例，您可能需要做一些完全不同的事情。

一旦两个容器都在运行，应用程序的测试就由步骤`sh "mvn verify"`执行。
这一步特定于 Java 和 Maven，同样与并行运行容器无关。

如果你想看看这是怎么回事，看看 https://github.com/ljpengelen/java-meetup-jwt 吧。
上面的例子是用于这个项目的 Jenkinsfile 的简化版本。