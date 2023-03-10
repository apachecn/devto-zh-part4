# Jacoco 的微服务代码覆盖率

> 原文：<https://dev.to/bhargavm/microservices-code-coverage-with-jacoco-4a36>

频繁部署是**微服务**架构的一大进步。如果您有一套在每次部署前运行的测试，并给出一个绿色的复选标记来放心地部署。我应该相信自动化测试吗？决定的一个方法是检查代码覆盖率。 **Jacoco** 帮助获得代码覆盖的细节。

Jacoco 是一个 java 库，您可以在您的应用程序中使用它来获取代码覆盖细节。Jacoco 代理安装在您的 jvm 上，该代理监听 jmx 度量并提供覆盖细节。输出可以是文件或套接字。我们可以配置文件的路径，当 jvm 启动时，它锁定文件，当 jvm 停止时，它将执行数据写入文件`jacoco.exec`。我们可以将该文件提供给 Sonar 之类的静态代码分析，它会显示结果或使用 jacoco reports 插件来生成报告。[https://www.jacoco.org/jacoco/trunk/doc/agent.html](https://www.jacoco.org/jacoco/trunk/doc/agent.html)

#### 上图显示了以下步骤

1) Jenkins 运行在一个虚拟机上，该虚拟机有自己的 jvm `JVM-A`，它构建应用程序并部署到远程主机上的容器中。

2)一旦应用程序在带有 jvm `JVM-B`的虚拟机上的容器中运行，其中需要安装和配置 jacoco 代理。根据配置，`JVM-B`上的 Jacoco 代理将执行数据发送到文件`(jacoco.exec)`。

3)从字面上看，步骤 3 可以是理解步骤 2 中生成的文件并生成报告的任何内容。如果我们提供了文件的路径，Sonar 就会这么做。Maven 有一个插件`jacoco:report`，它理解 jacoco.exec 文件并生成一些不错的 ui 报告。

#### 怎么做

我有一个例子

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[BhargavModepalli](https://github.com/BhargavModepalli)/[Spring-Boot-jacoco-Example](https://github.com/BhargavModepalli/Spring-Boot-jacoco-Example)

<article class="markdown-body entry-content container-lg" itemprop="text">

# spring-Boot-jaco co-示例

*   GET[http://localhost:8080/today](http://localhost:8080/today)给出今天的日期

*   跟随[https://www.jacoco.org/jacoco/trunk/doc/agent.html](https://www.jacoco.org/jacoco/trunk/doc/agent.html)来设置 jacoco codecoverage 以写入文件

*   构建[https://github . com/jaco co/jaco co/tree/master/org . jaco co . agent](https://github.com/jacoco/jacoco/tree/master/org.jacoco.agent)获取`jacocoagent.jar`

*   Java-Java agent:C:\ Bhargav \ jaco agent . jar = append = true，includes=*，output=file，destfile = C:\ Workspace \ Github \ Spring-Boot-jaco co-Example \ target \ jaco co . exec-jar Spring-Boot-jaco co-Example-0 . 0 . 1-snapshot . jar

</article>

[View on GitHub](https://github.com/BhargavModepalli/Spring-Boot-jacoco-Example)

which is simple rest service returns today's date with endpoint [http://localhost:8080/today](http://localhost:8080/today) and run the following command

#### 生成执行数据文件

`java -javaagent:C:\Bhargav\jacocoagent.jar=append=true,includes=*,output=file,destfile=C:\Workspace\Github\Spring-Boot-Jacoco-Example\target\jacoco.exec -jar Spring-Boot-Jacoco-Example-0.0.1-SNAPSHOT.jar`

**Java agent:**C:\ Bhargav \ jaco agent . jar:指向 jacocoagent jar 所在的位置。

**append :** 告诉 jacocoagent 如果有旧的指标，是否要用旧的指标附加新的指标

**include :** *意思是包括所有的类，我们可以限制到某个特定的包

**output :** 告诉 jacocoagent 你想把执行数据写到哪里，在这个例子中我们是告诉 jacocoagent 把它写到一个文件。

**destfile:**jaco agent 写入执行数据的文件的位置

将在上述命令的给定 destfile 路径中生成以下文件。

[![Add descriptionexecution data generated by jacoco agent](img/cd8500c2c946b9868ef9346f54d30f39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u_HOTccP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i6surq5u8t89txa5refi.png)

#### 从执行数据文件中生成报表

为了让这个文件可读，我们可以使用一个名为`jacoco-maven-plugin`的 maven 插件

 `<plugin>
<groupId>org.jacoco</groupId>
<artifactId>jacoco-maven-plugin</artifactId>
<version>0.8.3</version>
<executions>
<execution>
<goals>
<goal>report</goal>
</goals>
<configuration>
<dataFile>${project.build.directory}/jacoco.exec</dataFile>
<outputDirectory>${basedir}/target/jacoco</outputDirectory>
</configuration>
</execution>
</executions>
</plugin>` 

从 cmd 运行它

`mvn org.jacoco:jacoco-maven-plugin:0.8.3:report`

[![Add descriptionindex file<br>
](img/efa993c558b1ba51ec002e4a28953542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMgeOAdA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g08oz7hd0gkcqc3un4qq.png)

[![Add descriptionactual class with code coverage details](img/efa993c558b1ba51ec002e4a28953542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMgeOAdA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g08oz7hd0gkcqc3un4qq.png)