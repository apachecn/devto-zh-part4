# 为 Java InvocationHandler 实现添加对接口默认方法的支持

> 原文：<https://dev.to/allenball/adding-support-to-java-invocationhandler-implementations-for-interface-default-methods-h64>

Java 8 为接口引入了默认方法。现有的 [`InvocationHandler`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/InvocationHandler.html?is-external=true) 实现不会调用默认的接口方法。这篇短文记录了必要的变化。注意:它首先描述了基于阅读文档的实现，然后提供了一个 Java 8 的工作实现。

给出了 [`InvocationHandler`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/InvocationHandler.html?is-external=true) 实现:

```
 @Override
    public Object invoke(Object proxy,
                         Method method, Object[] argv) throws Throwable {
        Object result = null;
        /*
         * Logic to calculate result.
         */
        return result;
    } 
```

Enter fullscreen mode Exit fullscreen mode

[Java 8](https://www.java.com/en/download/help/java8.html) 解决方案似乎是通过 [`MethodHandle`](https://docs.oracle.com/javase/8/docs/api/java/lang/invoke/MethodHandle.html) :
来扩展实现以调用任何接口默认方法

```
 @Override
    public Object invoke(Object proxy,
                         Method method, Object[] argv) throws Throwable {
        Object result = null;
        Class<?> declaringClass = method.getDeclaringClass();

        if (method.isDefault()) {
            result =
                MethodHandles.lookup()
                .in(declaringClass)
                .unreflectSpecial(method, declaringClass)
                .bindTo(proxy)
                .invokeWithArguments(argv);
        } else {
            /*
             * Logic to calculate result.
             */
        }

        return result;
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果 [`Method`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html) 为“默认”，则调用目标接口方法。否则， [`InvocationHandler`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/InvocationHandler.html?is-external=true) 执行流程同前。任何接口默认方法*都应该被*调用:

1.  通过`MethodHandles.lookup().in(declaringClass)`找到 [`MethodHandles.Lookup`](https://docs.oracle.com/javase/8/docs/api/java/lang/invoke/MethodHandles.Lookup.html) ，
2.  通过`.unreflectSpecial(method, declaringClass)`得到一个 [`MethodHandle`](https://docs.oracle.com/javase/8/docs/api/java/lang/invoke/MethodHandle.html) ，绕过任何覆盖方法，
3.  用`.bindTo(proxy).invokeWithArguments(argv)`调用 [`proxy`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html) 上的方法

不幸的是，如果`declaringClass`对于调用者来说不是“私人可访问的”(大多数情况下是这样)，那么这就不起作用了，结果是:

```
Caused by: java.lang.IllegalAccessException: no private access for invokespecial: interface package1.SomeInterface, from package1.SomeInterface/public
    at java.lang.invoke.MemberName.makeAccessException(MemberName.java:850)
    at java.lang.invoke.MethodHandles$Lookup.checkSpecialCaller(MethodHandles.java:1572)
    at java.lang.invoke.MethodHandles$Lookup.unreflectSpecial(MethodHandles.java:1231)
    at package2.InvocationHandlerImpl.invoke(InvocationHandlerImpl.java:59) 
```

Enter fullscreen mode Exit fullscreen mode

实际的 [Java 8](https://www.java.com/en/download/help/java8.html) 方案是:

```
 @Override
    public Object invoke(Object proxy,
                         Method method, Object[] argv) throws Throwable {
        Object result = null;
        Class<?> declaringClass = method.getDeclaringClass();

        if (method.isDefault()) {
            Constructor<MethodHandles.Lookup> constructor =
                MethodHandles.Lookup.class.getDeclaredConstructor(Class.class);

            constructor.setAccessible(true);

            result =
                constructor.newInstance(declaringClass)
                .in(declaringClass)
                .unreflectSpecial(method, declaringClass)
                .bindTo(proxy)
                .invokeWithArguments(argv);
        } else {
            /*
             * Logic to calculate result.
             */
        }

        return result;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这在 [Java 9](https://www.oracle.com/java/java9.html) +中是行不通的。在 Java 9 和后续版本中，解决方案应该基于`MethodHandles.Lookup.findSpecial()`和/或`MethodHandles.privateLookupIn()`。