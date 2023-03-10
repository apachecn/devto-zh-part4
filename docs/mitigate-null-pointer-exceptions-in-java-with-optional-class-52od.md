# 用可选类减轻 Java 中的空指针异常

> 原文：<https://dev.to/vikneswaran/mitigate-null-pointer-exceptions-in-java-with-optional-class-52od>

空指针异常非常烦人，因为每个 java 开发人员都至少遇到过一次。随着代码的增长，处理这些空指针异常会因为大量的空检查而变得单调乏味。

java 8 中引入了 Optional 类，以提供一种类型级的解决方案来表示可选值，而不是使用空引用。java 8 之前的可选类是谷歌番石榴库的一部分。

让我们来看看用 *java.util.Optional* 类的一些实现

## 创建一个空对象

空的可选对象可以通过调用可选类的 *empty* 静态方法来创建。

```
@Test
public void createEmptyOptional() {
    Optional<String> empty = Optional.empty();
    assertFalse(empty.isPresent());//Also we can use the opposite one empty.isEmpty() from java 10
} 
```

*isPresent()* 只有在 Optional 中的对象不为 null 时才会返回 true

## 用对象创建可选类

可选类可以用 *Optional.of()* 或 *Optional.ofNullable()* 静态方法
创建

```
@Test
public void createOptionalObject() {
    String title = "Optional";
    Optional<String> opt = Optional.ofNullable(title);
    assertTrue(opt.isPresent());

    String title2 = null;
    Optional<String> opt1 = Optional.ofNullable(title2);
    assertFalse(opt1.isPresent());
} 
```

如果标题为空，Optional.of(title) 将抛出 NullPointerException。

## 用 Optional.ifPresent()访问对象时删除空检查

如果发现包装在可选中的对象不为空，它将使我们能够对包装的值运行一些代码。

```
public void checkOptionalIfPresent() {
    Color color = ColorFactoryProvider.getColor("red");
    Optional<Color> red = Optional.ofNullable(color);
    red.ifPresent(color -> System.out.println(color.getColorCode()));//wrapped code will get executed

    Color empty= null;
    Optional<Color> emptyColor = Optional.ofNullable(empty);
    emptyColor.ifPresent(color -> System.out.println(color.getColorCode()));// wrapped code will not get executed
} 
```

你能看到这个可选类如何让我们摆脱空检查，这样如果对象为空，被包装的代码就不会被执行吗？

## 从可选类中访问对象

可选类也有各种方法。

```
/* 
Optional.get() 
It returns the wrapped object.
it throws a no such element exception if the wrapped object is null.
*/
@Test
public void getValue(){
  Color color = ColorFactoryProvider.getColor("blue");
  Optional<Color> optionalColor= Optional.ofNullable(color);
  assertEquals(color, optionalColor.get());
}

/*
Optional.orElse()
It is like assigning default value for the object. 
if the wrapped object is null then default value will be returned.
*/
@Test
public void OptionalorElseCheck(){
  Color defaultColor = ColorFactoryProvider.getColor("red");
  Color blue = ColorFactoryProvider.getColor("blue");
  Optional<Color> optionalColor = Optional.ofNullable(blue);
  assertEquals(blue, optionalColor.orElse(defaultColor));

  Color empty = null;
  Optional<Color> emptyColor = Optional.ofNullable(empty);
  assertEquals(defaultColor, emptyColor.orElse(defaultColor));
} 
```

我们还有一些其他有用的方法
*Optional . orelseget()*——类似于 *orElse()* 唯一的区别是它接受一个供应商函数接口作为输入，并且仅当 Optional 中的包装器对象为空时才调用。

*Optional . orelsthrow()*-类似于 *orElse()* 唯一的区别是它接受异常对象作为输入，如果 Optional 中包装的对象为空，它将抛出给定的异常。

所以开始用可选的类来重构你的代码吧，它提供了在 java 中处理 null 的最佳实践。

欲了解更多详细信息，请查阅官方[文档](https://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html)