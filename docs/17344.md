# 面向初学者的 Java 编程资源集

> 原文：<https://dev.to/prahladyeri/a-collection-of-java-programming-resources-for-beginners-3o38>

*(最初发表于[https://github.com/prahladyeri/CuratedLists](https://github.com/prahladyeri/CuratedLists/blob/master/lists/java.md))*

注意:我已经很久没有更新这个列表了，所以非常欢迎反馈！

# Java 资源

## 公文等资源

*   [Java SE 官方文档](https://docs.oracle.com/javase/8/docs)
*   [Java SE 官方教程](https://docs.oracle.com/javase/tutorial/)
*   [Java 代码牧场-初学者学习资源](http://www.coderanch.com/how-to/java/JavaBeginnersFaq)
*   [Google - Java 开发工具](https://developers.google.com/java-dev-tools/)
*   [Maven Central——官方的 Java 包存储库](http://mvnrepository.com/)

## 库和框架

*   jUnit——Java 世界中的标准单元测试工具，`nunit`背后的灵感，相当于。网络世界。
*   Apache Commons-Java 世界中最受欢迎、使用率最高的库。
*   Guava -谷歌 java 核心库。
*   google-gson -有助于将 java 对象转换成 json，反之亦然。
*   Apache Tomcat——一个流行的 web 服务器和 Servlet 容器，Java 世界中托管和开发 web 应用的标准方式。官方对`Java EE`的替代。
*   [Apache TomEE](http://tomee.apache.org/apache-tomee.html)——碰巧 Tomcat 很棒，但它达不到 Oracle 对 web 托管平台的一些认证标准。因此`TomEE`被创建，尽管它的生产使用比`Tomcat`少得多。
*   [spring . io](https://spring.io)——一个全面的 Java 开源 web 框架，它开始解决`Java EE`开发方式中的一些主要缺点。它开创了 Java 世界中的依赖注入模式，其捆绑组件的模块化方法为 php 的 symfony 等其他框架提供了指导。
*   JHipster -一个开发平台，用于生成、开发和部署 Spring Boot + Angular Web 应用和 Spring 微服务。
*   [Apache log4j](http://logging.apache.org/log4j)——Java 世界的标准日志组件，启发了 C#的`log4net`和 PHP 的`log4php`。
*   hibernate-ORM -一个对象关系映射器。它用于在关系数据库中持久化数据。
*   mockito -模仿 java 框架。
*   selenium -各种工具和库，支持 web 浏览器自动化。
*   [Takes](http://www.takes.org/) - Takes 是一个开源的面向对象且不可变的 Java web 框架。
*   snmp4j -一个面向 Java 的行业标准 snmp 库。它允许通过其广泛的 API 创建 SNMP 代理和管理员。

## IDEs

*   [Eclipse](http://www.eclipse.org)——大多数 Java 专业人员使用的标准且最流行的 Java IDE，尤其是在企业中。尽管它看起来有些臃肿，有时很慢，设计也不太好，但它是目前 Java 世界中最好的。然而，它的优点远大于缺点——它有一个基于插件的模块化架构，不仅涵盖 Java，还涵盖 PHP、Python、C/C++和其他几种语言。还有一个 ADT 插件，用来开发 Android 应用。个人来说，超级粉丝！
*   Apache Netbeans -另一个伟大的 IDE，仅次于 Eclipse。这还有一个额外的优势，那就是得到了~~Oracle~~Apache Software Foundation(ASF)的支持，并且它内置了一个 Swing/JavaFX 设计器。然而，在支持广泛的项目时，它不如 Eclipse 灵活。它也缺少一个 python 插件，它的 android 插件也没有太多改进。
*   IntelliJ-Jetbrains Inc .开发的商业 IDE。鼓吹者声称它比 Eclipse 更好。
*   BlueJ -一个专门为向学生教授 Java 概念而设计的 IDE。它没有 Eclipse 和其他软件的所有花哨功能，只是一个简单的 IDE 来帮助理解 OOP 概念。
*   jEdit 更像是一个程序员的编辑器，而不是一个完全成熟的 IDE。尽管它缺少 Eclipse/Netbeans 中的 RAD 特性，但作为一个轻量级的文本编辑器，它工作得相当好。

## 工装

*   [Apache Ant](http://ant.apache.org/)——Java 世界中的标准构建测试工具，`nant`背后的灵感。净当量。通常与 Maven 或 Gradle 打包工具一起使用。
*   [Apache Maven](https://maven.apache.org)——Java 世界的标准包管理工具，尽管说实话，这个部门非常分散，错综复杂，`Gradle`成为非官方的替代物， [maven central](http://search.maven.org/) 不是唯一的官方来源。因为 Java 是企业驱动的，所以很多都有自己的 maven 中心。
*   Gradle——Maven 的强大而流行的替代品，尤其是因为它简洁的语法，可以让您摆脱 Maven 冗长的 XML。
*   Qulice 是一个 Java 质量监控工具，它可以对你的 Java 代码进行各种检查和验证。有一个 [maven 插件](http://www.qulice.com/qulice-maven-plugin/)是非常有用的。

## 在线教程、书籍和拼图

*   [WSIT 教程](https://docs.oracle.com/cd/E17802_01/webservices/webservices/reference/tutorials/wsit/doc/Examples_glassfish6.html)——关于使用核心 Java 工具如`wsimport`创建`WSDL`客户端的有用教程。
*   [Java 实践测试](http://www.javatpoint.com/examaccess)
*   [Reddit r/Java](http://www.reddit.com/r/java)
*   [甲骨文 Java 社区](https://home.java.net/forums)

## Java 8 中的新功能

*   [λ表达式](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-final.html)
*   [Lambda 相关库的增强功能](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-libraries-final.html)
*   [默认接口方法](http://zeroturnaround.com/rebellabs/java-8-explained-default-methods)
*   [并发更新](http://openjdk.java.net/jeps/155)
*   [新的日期时间 API (JSR 310)](http://sourceforge.net/apps/mediawiki/threeten/index.php?title=User_Guide)
*   [类型注释&可插拔类型系统](http://docs.oracle.com/javase/tutorial/java/annotations/type_annotations.html)