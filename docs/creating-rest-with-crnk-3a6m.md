# 使用 CRNK 创建 REST

> 原文：<https://dev.to/nchrisz/creating-rest-with-crnk-3a6m>

*注:这篇文章写于 2018 年冬季*，预计将于 2018 年冬季发表

微服务如今非常普遍，在 Travelex，我们在业务的各个方面都使用微服务。对于许多服务，我们一直在使用 Spring framework，以便快速启动和运行服务。由于所有的微服务都在相互通信，所以我们采用了 JSON:API 标准。构建在 JSON:API 标准之上的库是 CRNK 库。

Crnk 是一个原生的面向资源的 rest 库，其中资源、它们的关系和存储库是主要的构件。

将 Crnk 和 Spring 框架结合起来，我们可以快速启动和运行服务。Spring Boot 创建一个可运行的服务，CRNK 帮助处理 REST APIs 通信。本文和下一篇文章将介绍如何使用 CRNK 建立服务。我们将对 CRNK 有一个更一般的认识，稍后我们将讨论当我们想用 CRNK 做更高级的事情时所面临的一些挑战。

## CRNK

使用这个库的最低要求是 JAVA 8。对于这种设置，我们将只查看 gradle 和 maven。对于 maven，您可以在 pom.xml 中添加以下包:

```
<dependency>
  <groupId>io.crnk</groupId>
  <artifactId>crnk-setup-spring-boot2</artifactId>
  <version>${crnk.version}</version>
</dependency>
<dependency>
   <groupId>io.crnk</groupId>
   <artifactId>crnk-client</artifactId>
   <version>${crnk.version}</version>
</dependency>
<dependency>
   <groupId>io.crnk</groupId>
   <artifactId>crnk-validation</artifactId>
   <version>${crnk.version}</version>
</dependency>
<dependency>
   <groupId>io.crnk</groupId>
   <artifactId>crnk-security</artifactId>
   <version>${crnk.version}</version>
</dependency>
<dependency>
   <groupId>io.crnk</groupId>
   <artifactId>crnk-jpa</artifactId>
   <version>${crnk.version}</version>
</dependency>
<dependency>
   <groupId>io.crnk</groupId>
   <artifactId>crnk-monitor-brave4</artifactId>
   <version>${crnk.version}</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

你为 gradle 构建了 gradle 如下:

```
compile project(':crnk-setup:crnk-setup-spring-boot2')
compile project(':crnk-jpa')
compile project(':crnk-validation')
compile project(':crnk-home')
compile project(':crnk-ui')
compile project(':crnk-operations')
compile project(':crnk-security')
compile project(':crnk-data:crnk-data-facet') 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，项目就可以使用 CRNK 了。使用 CRNK 非常容易。作为最低要求，它只需要一个模型类注释和一个与该模型类相连的存储库。存储库将处理所述模型的数据。它可以将数据作为散列表存储在自身中，也可以将数据存储在一个数据结构中，例如数据库。

## 模型和数据

模型是应用程序拥有的数据的表示。数据需要存储在存储库中，存储库要么将数据保存在内存中，要么可以连接到数据库。

```
// Copyright (c) 2018 Travelex Ltd
package com.travelex.store.model;
import io.crnk.jpa.annotations.JpaResource;
import lombok.Data;
import javax.persistence.Entity;
@Data
@JpaResource(type = "person")
@Entity
public class Person {
  private String name;
  private int age;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，当发出 POST、PUT 或 GET 请求时，JpaResource 将是这个资源的外部连接。注释实体将把我们直接连接到数据库，作为我们保存数据的底层结构。如果我们用 using 实体配置，我们不需要构造一个存储库类。

### 龙目岛

Lombok 包是我们在整个服务中使用的，它极大地帮助我们减少了代码。lombok 工作方式是为我们的类生成代码，这样我们就不必编写 getters 和 setters 以及所有那些类应该拥有的方法。数据的注释捆绑了不同的注释来创建 getter、setter、toString、constructors 等等，所以我们只需要定义模型的属性。

## 储存库

我们现在需要将我们的资源连接到它的数据，我们将通过创建一个存储库来完成这个任务。为了简单起见，我们的存储库将我们的数据存储在内存中的一个散列表中。

一个资源，也就是模型，需要连接到它的数据。这通常是通过存储库来完成的。存储库要么将数据作为 hashmap 保存在内存中，要么可以连接到一个数据结构，例如数据库。一个普通的存储库可能是这样的:

```
// Copyright (c) 2018 Travelex Ltd
package com.travelex.store.model;
import io.crnk.core.queryspec.QuerySpec;
import io.crnk.core.repository.ResourceRepositoryBase;
import io.crnk.core.resource.list.ResourceList;
import org.springframework.stereotype.Component;
import java.util.HashMap;
import java.util.Map;
@Component
public class PersonRepository extends ResourceRepositoryBase<Person, int> {
  private Map<int, String> list = new HashMap<>();
  public PersonRepository() {
    super(Person.class);
  }

  @Override
  public void delete(int age) {
    list.remove(age);
  }

  @Override
  public <S extends Person> S save(S person) {
    list.put(person.getAge(), person.getName());
  }

  @Override
  public ResourceList<Person> findAll(QuerySpec querySpec) {
    return querySpec.apply(list.values());
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 过滤和 CRNK

JpaResource 为我们提供了来自 CRNK 的如何查询数据的特性。默认选项是过滤和排序资源。通过过滤，您可以拥有:EQ 表示相等的值，如模式，LT 表示小于，LE 表示小于等于，GT 表示大于，GE 表示大于等于。典型的过滤器调用

```
/person?filter[EQ][age]=25 
```

Enter fullscreen mode Exit fullscreen mode

将在数据库中查找年龄数据等于 25 的每个实体。

只要正确处理存储库中的查询，您就可以添加自己的过滤操作符。这意味着 filterOperators 既存在于进入服务器的请求中，也存在于对数据库的查询请求中。这将在以后处理。

## 总结

使用 CRNK 库，您可以轻松创建 REST APIs。它有助于为服务构建 REST API，我们唯一需要担心的是我们需要服务处理的数据。如果你使用 Spring 框架，就像我们在测试服务中所做的那样，你至少需要不到 5 个类:启动 SpringBoot 应用程序的主类，代表我们数据的模型类和连接数据库的配置类。

CRNK 有利于创建可以保存数据的简单 REST APIs 服务。SpringBoot 很容易使用，作为开发人员，我们不需要关注代码来连接我们的服务。但是就像 CRNK 与 SpringBoot 连接和设置服务很简单一样，如果你需要配置一些 CRNK 中不标准的东西，你就需要绕开它。