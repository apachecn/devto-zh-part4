# 实用主义应用:避免单一实现接口

> 原文：<https://dev.to/henritremblay/pragmatism-applied-avoid-single-implementation-interface-kll>

很久以前(想想 2000 年)，Java 中的所有类都有一个接口。
你先从`MyInterface`开始，然后加了一个`MyInterfaceImpl`。

这导致了许多样板文件和调试的烦恼。我曾经用代码生成器来简化它。

我们为什么要这么做？

两个原因。一个坏的一个好的。

## 不好的原因是脱钩

这个想法是，如果你依赖一个接口，你可以在需要的时候交换实现。

这是一个“你以后可能需要它”的问题。每一个有“可能”和“以后”的句子都应该换成“我不在乎”。因为大多数时候，“以后”永远不会发生，你只是在浪费时间和精力以防万一。
以后无论发生什么都应该以后再处理。

也就是说，你可能会争辩说“是的，但以后处理起来会痛苦得多”。好的。让我们检查一下。

假设你有一些奶酪

```
public class Cheese {

  private final String name;

  public Cheese(String name) {
    this.name = Objects.requireNonNull(name);
  }

  public String getName() {
    return name;
  }
} 
```

然后，您想从数据库中检索奶酪。

```
public class CheeseDao {

  private final Database database;

  public Cheese findByName(String name) {
    return database.names()
        .filter(name::equals)
        .reduce((a, b) -> {
          throw new IllegalStateException("More than one entry found for " + name);
        })
        .map(Cheese::new)
        .orElse(null);
  }
} 
```

然后你有一个依赖于`CheeseDAO`的 REST 资源。

```
public class CheeseResource {

  private final CheeseDAO cheeseDAO;

  public CheeseResource(CheeseDAO cheeseDAO) {
    this.cheeseDAO = cheeseDAO;
  }

  public Cheese get(String name) {
    return cheeseDAO.findByName(name);
  }
} 
```

因为你是一个有效率的人，所以你决定`CheeseDAO`不需要界面。到目前为止，它只有一个实现，而且你还没有构建一个 cheese 开源库。
所有这些代码都在你的小奶酪应用程序里。

但是有一天，一些需求来了，你确实需要另一个实现。
“后来”果然发生了。

所以你现在把`CheeseDAO`变成了一个接口。

```
public interface CheeseDao {
  Cheese findByName(String name);
}

public class CheeseDatabaseDao implements CheeseDao {

  private final Database database;

  public Cheese findByName(String name) {
    return database.names()
        .filter(name::equals)
        .reduce((a, b) -> {
          throw new IllegalStateException("More than one entry found for " + name);
        })
        .map(Cheese::new)
        .orElse(null);
  }
} 
```

现在，你可以根据`CheeseDAO`修复所有类的编译错误了。

例如，您将`CheeseResource`修改为:

```
public class CheeseResource {

  private final CheeseDAO cheeseDAO;

  public CheeseResource(CheeseDAO cheeseDAO) {
    this.cheeseDAO = cheeseDAO;
  }

  public Cheese get(String name) {
    return cheeseDAO.findByName(name);
  }
} 
```

我给你 5 秒钟。1, 2, 3, 4, 5.

是的，我在逗你。什么都没有改变。
不是单个字符。

“以后”把一个类变成一个接口并不痛苦。

所以我称之为糟糕的理由。现在做是痛苦的，以后没有好处。

## 现在，好的理由:测试

具体类的问题是你需要实例化。在测试环境中，你想要模仿依赖关系。为了模仿一个具体的类，你需要两样东西

1.  扩展该类以便能够模拟该行为
2.  实例化该类

第一个要求很简单，第二个要求更复杂。如果这个类很简单，并且有一个简单的构造函数可以调用，那么一切都没问题。如果类实例化很烦人，你就有问题了。

这就是我介入的地方。最酷的技巧是实例化类而不调用任何构造函数。

幸运的是，Java 允许这样做。因为序列化一直都在这么做。你只需要在引擎盖下偷偷摸摸一点点。

最初，我参与开源是为了解决这个问题。今天大多数模仿框架都在使用 [Objenesis](http://objenesis.org/) 来执行这项任务。
我在[的上一篇文章](http://blog.tremblay.pro/2015/02/the-history-of-partial-mocking.html)中提到过。

所以，从 2003 年开始，你不需要害怕使用具体的类作为依赖。你可以像模仿任何界面一样模仿它们。