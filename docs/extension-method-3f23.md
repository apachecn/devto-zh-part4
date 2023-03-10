# 扩展方法

> 原文：<https://dev.to/realvino/extension-method-3f23>

在 C#中，扩展方法概念允许您在现有类或结构中添加新方法，而无需修改原始类型的源代码，并且不需要原始类型的任何特殊权限，也不需要重新编译原始类型。它是在 C# 3.0 中引入的。让我们借助一个例子来讨论这个概念。假设您有一个包含三个方法的类或结构，并且您想要在该类或结构中添加两个新方法，您没有该类/结构的源代码，或者没有该类/结构的权限，或者该类是一个密封类，但是您仍然想要在其中添加新方法，那么您可以使用概念扩展方法在现有的类/结构中添加新方法。现在您创建了一个新的静态类，它包含了您想要添加到现有类中的两个方法，现在将这个类与现有类绑定。绑定后，您将看到现有的类可以访问两个新添加的方法。如下面的程序所示。

示例:首先，我们在 Program1.cs 文件中创建一个名为 TestClass 的类。它包含三种方法，即 M1()、M2()和 M3()。

// C#程序来说明概念

//的扩展方法

使用系统；

namespace extension method {
//这里 TestClass 类包含三个方法
//现在我们要在它里面再添加两个新方法

//不重新编译这个类
class TestClass {

//方法 1
public void M1()

{
控制台。WriteLine("方法名:M1 ")；
}
//方法二
公共 void M2()
{
控制台。WriteLine("方法名:M2 ")；
}
//方法 3
public void M3()
{
控制台。WriteLine("方法名:M3 ")；
}

}

}

现在我们在 Program2.cs 文件中创建一个名为 NewMethodClass 的静态类。它包含两个方法，即 M4()和 M5()。现在我们想在 TestClass 类中添加这些方法，所以我们使用 binding 参数将这些方法与 TestClass 类绑定。之后，我们创建了另一个名为 GFG 的类，其中 TestClass 类访问了所有的五个方法。

// C#程序来说明概念
//的扩展方法

使用系统；

namespace extension method {
//这个类包含了我们要添加到 TestClass 类中的 M4 和 M5 方法
//方法。
// NewMethodClass 是静态类
静态类 NewMethodClass {

```
// Method 4 
public static void M4(this TestClass g) 
{ 
    Console.WriteLine("Method Name: M4"); 
} 

// Method 5 
public static void M5(this TestClass g, string str) 
{ 
    Console.WriteLine(str); 
} 
```

}

//现在我们创建一个新类，其中
// TestClass 类访问所有五个方法

公共类 GFG {

```
// Main Method 
public static void Main(string[] args) 
{ 
    TestClass g = new TestClass(); 
    g.M1(); 
    g.M2(); 
    g.M3(); 
    g.M4(); 
    g.M5("Method Name: M5"); 
} 
```

}
}
输出:
方法名:M1
方法名:M2
方法名:M3
方法名:M4
方法名:M5
要点:
在这里，绑定参数是用来将新方法与现有的类或结构绑定的那些参数。当您调用扩展方法时，它不取任何值，因为它们仅用于绑定，而不用于任何其他用途。在扩展方法的参数列表中，绑定参数总是出现在第一个位置。如果将绑定参数写入第二个、第三个或任何其他位置，而不是第一个位置，编译器将会给出错误。绑定参数是使用该关键字创建的，后跟要添加新方法的类的名称和参数名称。例如:

这里，这个关键字用于绑定，TestClass 是要绑定的类名，g 是参数名。扩展方法总是被定义为静态方法，但是当它们与任何类或结构绑定时，它们将转换为非静态方法。
当使用与现有方法相同的名称和签名定义扩展方法时，编译器将打印现有方法，而不是扩展方法。或者换句话说，扩展方法不支持方法覆盖。
您也可以使用扩展方法概念在密封类中添加新方法。
它不能应用于字段、属性或事件。
必须在顶级静态类中定义。
不允许多个绑定参数意味着一个扩展方法只包含一个绑定参数。但是你可以在扩展方法中定义一个或多个普通参数。
优点:
扩展方法的主要优点是在现有的类中添加新的方法，不需要使用继承。
您可以在现有类中添加新方法，而无需修改现有类的源代码。
它也可以处理密封类。