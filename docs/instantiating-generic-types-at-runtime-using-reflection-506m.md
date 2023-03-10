# 使用反射在运行时实例化泛型类型

> 原文：<https://dev.to/emanuelvintila/instantiating-generic-types-at-runtime-using-reflection-506m>

在这篇文章中，我们将看到如何在运行时使用**反射**实例化泛型类型，并使用正确的参数调用实例上的方法。

假设您想要在运行时动态创建一个`List<T>`；我们不想创建一个`List<object>`实例，我们希望我们的实例有一个**运行时类型**的`List<T>`。当然，根据用户的选择，T 将被替换为实际类型，比如`string`或`int`。

*注意:没有异常处理；这超出了帖子的范围。*

```
using System;
using System.Collections;
using System.Collections.Generic;
using System.Reflection;

namespace Reflection
{
    public class Program
    {
        private static void Main(string[] args)
        {
            Console.WriteLine("What type do you want your items to be? ");
            string input = Console.ReadLine();
            Type type = Type.GetType(input);

            // this is called an unbound type
            Type unbound = typeof(List<>);
            // unbound.IsGenericType == true
            Type bound = unbound.MakeGenericType(type);

            // get parameterless constructor and invoke it
            object list = bound.GetConstructor(new Type[0]).Invoke(new object[0]);

            // cannot use nameof with unbound types
            // string addMethod = nameof(List<>.Add);
            // use any bound type instead
            string methodNameAdd = nameof(List<object>.Add);
            MethodInfo methodAdd = bound.GetMethod(methodNameAdd);

            while (true)
            {
                Console.Write("Insert a new value into the list: ");
                input = Console.ReadLine();
                object arg = input;
                if (type != typeof(string))
                {
                    ConstructorInfo constructor = type.GetConstructor(new[] {typeof(string)});
                    // construct the type from a string
                    if (constructor != null)
                        arg = constructor.Invoke(new object[] {input});
                    // or call the static Parse method on C# primitive types
                    else
                        arg = type
                            // get the public static Parse method that accepts a string
                            .GetMethod("Parse", 0, BindingFlags.Static | BindingFlags.Public, null,
                                new[] {typeof(string)}, null)
                            .Invoke(null, new object[] {input});
                }

                methodAdd.Invoke(list, new[] {arg});
                // display the list after adding the item
                foreach (object item in (IEnumerable) list)
                    Console.Write($"{item} ");
                Console.WriteLine();
            }
        }
    }
} 
```

我们已经指定了`list`变量的**编译时**类型为`object`，但是它将被赋予一个`List<T>`，该变量将使用反射创建。

我们让用户选择他们想要的列表类型。这应该被指定为一个**完全限定名**(例如`System.Int32`)。然后我们向列表中添加条目，首先将它们转换成适当的类型。