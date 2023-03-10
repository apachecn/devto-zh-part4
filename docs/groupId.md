# 将 revision 特性与 maven 一起使用以保持版本一致性

> [https://dev . to/earrooron/使用修订-con-maven-to-keep-consistency-de-version-4ih8](https://dev.to/earroyoron/utilizar-revision-con-maven-para-mantener-coherencia-de-versiones-4ih8)

作为对我上一篇文章[的一个小扩展，正确的版本化和建立依赖关系](https://dev.to/earroyoron/la-forma-correcta-de-versionar-y-establecer-dependencias-4499)我将指出我们在 maven 的帮助，从 3.5.0 版开始

在此构造工具中，我们可以指定一个属性`revision`，并将其用于*父项*，并在所有模块*子*中继承该属性，这样我们就可以轻松地应用*部署版本一致性的原则*

这是我们会放在父母身上的:

```
 <groupId>io.earroyoron</groupId>
  <artifactId>project-parent</artifactId>
  <packaging>pom</packaging>
  <version>${revision}</version>
  <properties>
    <revision>1.3.0</revision>
  </properties> 
```

在下面的模块中，我们将指出该属性:

```
 <parent>
    <groupId>io.earroyoron</groupId>
    <artifactId>project-parent</artifactId>
    <version>${revision}</version>
  </parent>

  <groupId>io.earroyoron</groupId>
  <artifactId>child-module-a</artifactId>
  <version>${revision}</version> 
```

如我所述，主要优点是保持的版本一致性，即在该父项下的所有组件中均为 1.3.0(应如此)。

如果您不确定这是否正确，请查看使用此架构的源，例如 Spring Boot。

## 好货？

我的老伙伴和朋友[【Marcos martínez @ cedello Marcos】](https://twitter.com/cedillomarcos)提醒我，在使用 *Maven* 进行版本管理时，使用 BOM 也是必不可少的，但我将把这个话题留待下次发表。