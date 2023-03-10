# 如何用 Java (Eclipse)中的 JUnit 5 编写单元测试

> 原文：<https://dev.to/yongliang24/how-to-write-unit-tests-with-junit-5-in-java-eclipse-341h>

## JUnit 是什么？

JUnit 是一个 Java 测试框架。它既可以用于单元测试，也可以用于 UI 测试。这是一个很好的工具，使用起来很有趣。在本文中，我将向初学者演示如何在 Eclipse IDE 中使用这个框架。

Junit 5 附带了 Eclipse，所以我们需要做的就是在第一次创建项目时将它添加到我们的项目中。

步骤:

1.  将 Junit 添加到项目中。
2.  添加新文件->创建新的类文件。
3.  添加新文件->创建 JUnit 测试文件。
4.  在类中写一些方法。
5.  在 Junit 测试文件中创建一个该类的对象。
6.  使用对象调用类方法来测试输入和输出。

```
//This class has one method which will return a string argument.
public class HelloWorld {

     public String printThis(String str){

               return str;
        }

} 
```

```
//The JUnit file would look like this

class HelloWord{
//instantiate a HelloWorld object called hello.
   HelloWorld hello = new HelloWorld();

//test method names tends to be descriptive 
   @Test
   void should_return_a_same__passing_string_value(){

//one way to think about constructing a test is use these 3 steps

       //given (the inputs)
       String cake = "chocolate cake";

       //when (the actions)
       String cakeTest = "chocolate cake";

       //then (the results)

       assertEquals(cakeTest, hello.printThis(cake) );
//this will pass the test if both values are equal.
 }
} 
```

这些是执行最基本的单元测试的步骤。