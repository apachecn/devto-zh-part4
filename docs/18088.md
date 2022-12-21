# 那些卑鄙的 NullPointerExceptions！

> 原文：<https://dev.to/joshaustintech/those-sneaky-nullpointerexceptions-580c>

有一种驾驶方式叫做“防御性驾驶”。防御性司机预见事故和糟糕的司机。这降低了发生事故的几率。它使我避免了几次严重的事故。

类似地，如果我们知道如何预测，我们可以避免 Java 中可怕的`NullPointerException`。

比如这个`if`检查有什么问题？

```
if (aString.equals("yes")) { /* do stuff */ } 
```

Enter fullscreen mode Exit fullscreen mode

假设`aString`已经初始化。如果一个字符串没有初始化，就会是`null`！在一个`null`对象上调用`.equals()`不会有好结果。这种情况比你想象的更常见。不如这样写:

```
if ("yes".equals(aString)) { /* do stuff */ } 
```

Enter fullscreen mode Exit fullscreen mode

我最近编辑了一个 POJO，用来表示一个反序列化的 JSON 对象。业务需求表明我需要删除任何前导或尾随空格。谢天谢地，`String.trim()`方法可以在一行中为我做到这一点！我把它放在 setter 中，就像这样:

```
public class Person {
  private String firstName;

  public String getFirstName() { return this.firstName; }

  public void setFirstName(String firstName) { 
    this.firstName = firstName.trim(); 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来很无辜。😉

想象一下，当将一个 JSON 转换成这个对象时，抛出一个`com.fasterxml.jackson.databind.JsonMappingException`并给出一个不太有用的解释“N/A”时，我有多沮丧。发生了什么事？？？

很明显，如果在 JSON 中`firstName`是`null`，`setFirstName(firstName)`正在对`null`对象调用`trim()`！

我需要做的是检查以确保`setFirstName(firstName)`中的字符串参数不为空。检查的方法不止一种，但是我选择了一个三元运算符:

```
public class Person {
  private String firstName;

  public String getFirstName() { return this.firstName; }

  public void setFirstName(String firstName) { 
    this.firstName = (firstName == null) ? null : firstName.trim(); 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你呢？你最近遇到可怕的`NullPointerException`了吗？你是怎么修好的？